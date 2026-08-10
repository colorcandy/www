package jp.co.nttcom.kosei.batch.cf.cfh35;

import jp.co.nttcom.kosei.batch.common.cf.util.CfBatchUtils;
import jp.co.nttcom.kosei.batch.ss.constants.KoseiBatchCodeConstants.KoseiExitCode;
import jp.co.nttcom.kosei.batch.ss.core.KoseiTasklet;
import jp.co.nttcom.kosei.batch.ss.exception.KoseiBatchException;
import jp.co.nttcom.kosei.batch.ss.message.KoseiBatchSSMessageId;
import jp.co.nttcom.kosei.common.cf.message.CfBatchMessageId;
import jp.co.nttcom.kosei.common.cf.util.CfCommonUtils;
import jp.co.nttcom.kosei.common.ss.batch.params.CFH3501JobParams;
import jp.co.nttcom.kosei.common.ss.exception.KoseiFileException;
import jp.co.nttcom.kosei.common.ss.exception.KoseiSystemException;
import jp.co.nttcom.kosei.common.ss.file.model.KoseiVariableDto;
import jp.co.nttcom.kosei.common.ss.file.service.KoseiVariableItemWriter;
import jp.co.nttcom.kosei.common.ss.logger.KoseiLogger;
import jp.co.nttcom.kosei.common.ss.logger.KoseiLoggerInject;
import jp.co.nttcom.kosei.common.ss.message.KoseiMessageAccessor;
import jp.co.nttcom.kosei.common.ss.util.KoseiFileUtils;

import java.io.File;
import java.nio.file.Path;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.ArrayList;
import java.util.List;

import jakarta.inject.Inject;

import org.springframework.batch.core.StepContribution;
import org.springframework.batch.core.configuration.annotation.StepScope;
import org.springframework.batch.core.scope.context.ChunkContext;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.dao.DataAccessException;
import org.springframework.stereotype.Component;

/**
 * <p>[概 要] 標準報酬算入依頼ファイル作成機能</p>
 * <p>[詳 細] <br>
 * 標準報酬算入依頼ファイル作成機能の処理を実行する</p>
 * <p>[備 考] Terasoluna Batch 5系 Taskletモデルへ移行済み。旧実装は {@link CFH3501BLogic} を参照。</p>
 * <p>[環 境] JRE 21</p>
 * <p>Copyright © NTT DOCOMO SOLUTIONS, Inc.</p>
 *
 * @version 1.0
 * @author ビジネストランスフォーメーション本部 コーポレートビジネスソリューション部
 */
@Component
@StepScope
public class CFH3501Tasklet extends KoseiTasklet<CFH3501JobParams> {

    /**
     * メッセージ出力に利用するログ機能を提供するインタフェース
     */
    @KoseiLoggerInject
    private KoseiLogger logger;

    /**
     * 外部情報取得（標準報酬算入依頼ファイル（全件））用出力ディレクトリ
     */
    @Value("${CFH3501.download.cfh35f01.dir}")
    private Path downloadCfh35f01Dir;

    /**
     * 外部情報取得（標準報酬算入依頼ファイル（会社コード別））用出力ディレクトリ
     */
    @Value("${CFH3501.download.cfh35f02.dir}")
    private Path downloadCfh35f02Dir;

    /**
     * メッセージ文を取得するインタフェース
     */
    @Inject
    private KoseiMessageAccessor koseiMessageAccessor;

    /**
     * CSVファイル拡張子
     */
    private static final String FILE_CSV = ".csv";

    /**
     * 標準報酬算入依頼ファイル（全件）ファイル名
     */
    private static final String CFH35F01 = "000000_CFH35F01_";

    /**
     * 標準報酬算入依頼ファイル(会社別)ファイル名
     */
    private static final String CFH35F02 = "_CFH35F02_";

    /**
     * 全社ディレクトリ
     */
    private static final String ALL_DIR = "999999";

    /**
     * 会社別ディレクトリ
     */
    private static final String KAISHA_DIR = "kaishaCd";

    /**
     * 標準報酬算入依頼ファイル作成DAOインターフェース
     */
    @Inject
    private CFH35Dao cfh35Dao;

    /**
     * 業務内共通クラス
     */
    @Inject
    private CfCommonUtils cfCommonUtils;

    /**
     * カフェテリア 共通 ユーティリティ クラス
     */
    @Inject
    private CfBatchUtils cfBatchUtils;

    /**
     * 標準報酬算入依頼ファイル出力件数
     */
    private int houshuOutputCount;

    /**
     * 給与接続走行年月
     */
    private String kyuyosetuzokuSkom;

    /** {@inheritDoc} */
    @Override
    protected Class<CFH3501JobParams> getParamDtoClass() {
        return CFH3501JobParams.class;
    }

    /**
     * <p>[概 要] 標準報酬算入依頼ファイル作成機能のメイン処理</p>
     * <p>[詳 細] <br>
     * 標準報酬算入依頼ファイル作成機能のメイン処理を実行する</p>
     * <p>[備 考] </p>
     *
     * @param params ジョブパラメータ
     * @param contribution ステップ実行情報
     * @param chunkContext チャンクコンテキスト
     * @return ジョブ終了コード
     * @throws Exception 例外
     */
    @Override
    protected KoseiExitCode execute(CFH3501JobParams params,
            StepContribution contribution, ChunkContext chunkContext) throws Exception {

        // --1.初期処理--START

        // （1）業務日付を取得する
        LocalDate wkDate = cfCommonUtils.getDate();
        // （2）日時ワークに日時設定
        LocalDateTime wkDateTime = this.cfCommonUtils.getDateTime();
        try {
            // （3）CFH35Dao#selecrCfh35KyusetuSokom1を呼び出す
            kyuyosetuzokuSkom = this.cfh35Dao.selecrCfh35KyusetuSokom1();
        } catch (DataAccessException e) {
            // 参照エラーが発生した場合、例外を出力する
            throw new KoseiSystemException(
                    koseiMessageAccessor.getText(KoseiBatchSSMessageId.SSB00F003), e);
        }

        // --1.初期処理--END

        // --2.主処理--START

        // （1）標準報酬算入依頼ファイル情報Dtoリストを生成
        List<Cfh35RewardDataDto> cfh35RewardDataDtoList;
        try {
            // (2)CFH35Dao#selectCfh35RewardData1を呼び出す
            cfh35RewardDataDtoList = this.cfh35Dao.selectCfh35RewardData1(kyuyosetuzokuSkom);
        } catch (DataAccessException e) {
            // 参照エラーが発生した場合、例外を出力する
            throw new KoseiSystemException(
                    koseiMessageAccessor.getText(KoseiBatchSSMessageId.SSB00F003), e);
        }
        if (cfh35RewardDataDtoList.isEmpty()) {
            // （3）標準報酬算入依頼ファイル情報が空だった場合、空の標準報酬算入依頼ファイルを作成する
            // 作成時刻ファイル名を取得する
            String sysFname =
                    this.cfCommonUtils.getJPyyyyMMddHHmmssSSSFormatter(wkDateTime)
                            + FILE_CSV;
            // 空のファイル情報格納オブジェクトリスト生成
            List<CFH35F01Dto> fileDtoList = new ArrayList<>();
            // 標準報酬算入依頼ファイル作成処理を呼び出す
            this.execFile(downloadCfh35f01Dir.toString(), CFH35F01 + sysFname, ALL_DIR, fileDtoList);
            // ファイルレコード件数(0件)をログへ出力する
            this.logger.log(CfBatchMessageId.CFH35I001, "標準報酬算入依頼ファイル",
                    this.houshuOutputCount, wkDate);
            // ジョブ終了コードを返却する
            return KoseiExitCode.COMPLETED;
        }

        // （4）集約処理(summary)を呼び出し、標準報酬算入依頼ファイル出力Dtoを作成する。
        List<CFH35F01Dto> fileDtoForShainList = summary(cfh35RewardDataDtoList);

        // （5）日時ワークより作成時刻を取得し、ファイル名を取得する
        String sysFname =
                this.cfCommonUtils.getJPyyyyMMddHHmmssSSSFormatter(wkDateTime) + FILE_CSV;

        // （6）全社共通標準報酬算入依頼ファイル出力を作成する。
        this.execFile(downloadCfh35f01Dir.toString(), CFH35F01 + sysFname, ALL_DIR,
                fileDtoForShainList);

        // （7）会社別標準報酬算入依頼ファイル出力を作成する。
        this.execFile(downloadCfh35f02Dir.toString(), CFH35F02 + sysFname, KAISHA_DIR,
                fileDtoForShainList);

        // --2.主処理--END

        // --3.後処理--START

        // （1）ファイルレコード件数をログに出力する。
        this.logger.log(CfBatchMessageId.CFH35I001, "標準報酬算入依頼ファイル", this.houshuOutputCount,
                wkDate);

        // （2）ジョブ終了コードを返却する。
        return KoseiExitCode.COMPLETED;

        // --3.後処理--END
    }

    /**
     * <p>[概 要] 標準報酬算入依頼ファイル作成処理</p>
     * <p>[詳 細] <br>
     * 標準報酬算入依頼ファイル作成処理を行う</p>
     * <p>[備 考] </p>
     *
     * @param outputDir 出力ディレクトリ
     * @param outputFName 出力ファイル名
     * @param mkDir 作成ディレクトリ項目名
     * @param fileDtoList 標準報酬算入依頼ファイル情報Dtoリスト
     * @throws KoseiBatchException ファイル出力エラーが発生した場合
     */
    private void execFile(String outputDir, String outputFName, String mkDir,
            List<CFH35F01Dto> fileDtoList) {

        // --1.初期処理--START

        // （1）標準報酬算入依頼ファイル出力件数を初期化する
        this.houshuOutputCount = 0;

        // （2）作成ファイル名を初期化する
        String fName = "";

        // --1.初期処理--END

        // --2.ファイル出力処理--START

        KoseiVariableItemWriter<CFH35F01Dto, KoseiVariableDto.None,
                KoseiVariableDto.None, KoseiVariableDto.None> fileWriter = null;
        try {
            // （1）標準報酬算入依頼ファイル出力Dtoリストが空の場合
            if (fileDtoList.isEmpty()) {
                // （ア）makeFileを呼び出す
                fileWriter = this.makeFile(outputDir, outputFName, mkDir, ALL_DIR);
                // （イ）作成ファイル名に"全社共通"を設定する
                fName = ALL_DIR;
            }

            // （2）標準報酬算入依頼ファイル出力Dtoリストが空でない場合
            for (CFH35F01Dto cfh35F01Dto : fileDtoList) {
                switch (mkDir) {
                    // （ア）全社ファイル作成の場合
                    case ALL_DIR:
                        if (!ALL_DIR.equals(fName)) {
                            // a.fileWriterをクローズする
                            KoseiFileUtils.closeQuietly(fileWriter);
                            // b.makeFileを呼び出す
                            fileWriter = this.makeFile(outputDir, outputFName, mkDir,
                                    ALL_DIR);
                            // c.作成ファイル名に"全社共通"を設定する
                            fName = ALL_DIR;
                        }
                        break;
                    // （イ）会社別ファイル作成の場合
                    case KAISHA_DIR:
                        // a.標準報酬算入依頼ファイル出力Dtoリスト．給与支給会社コードとファイル名が異なる場合
                        if (!cfh35F01Dto.getKyuyoSikyuKaisyaCd().equals(fName)) {
                            // a.fileWriterをクローズする
                            KoseiFileUtils.closeQuietly(fileWriter);
                            // b.makeFileを呼び出す
                            fileWriter = this.makeFile(outputDir, outputFName, mkDir,
                                    cfh35F01Dto.getKyuyoSikyuKaisyaCd());
                            // c.作成ファイル名に給与支給会社コードを設定する
                            fName = cfh35F01Dto.getKyuyoSikyuKaisyaCd();
                        }
                        break;
                    default:
                        break;
                }

                // （ウ）ファイルにデータを書き込む
                if (fileWriter != null) {
                    fileWriter.writeData(cfh35F01Dto);
                    // 標準報酬算入依頼ファイル出力件数をカウントアップする。
                    this.houshuOutputCount++;
                }
            }

        } catch (KoseiFileException e) {
            // ファイル出力エラーが発生した場合、例外を出力する
            throw new KoseiBatchException(KoseiBatchSSMessageId.SSB00E008, e, e.getFileName());
        } finally {
            // 強制実行処理（finally）
            KoseiFileUtils.closeQuietly(fileWriter);
        }

        // --2.ファイル出力処理--END
    }

    /**
     * <p>[概 要] 標準報酬算入依頼ファイルデータ処理</p>
     * <p>[詳 細] <br>
     * 標準報酬算入依頼ファイルデータ処理
     * </p>
     * <p>[備 考] </p>
     *
     * @param list 標準報酬算入依頼ファイル情報Dtoリスト
     * @return 標準報酬算入依頼ファイル出力オブジェクトDtoリスト
     */
    private List<CFH35F01Dto> summary(List<Cfh35RewardDataDto> list) {

        // --１．初期化処理--START

        // (1)返却用オブジェクトリストのインスタンス生成
        List<CFH35F01Dto> retList = new ArrayList<>();

        // --１．初期化処理--END

        // --２．社員単位処理--START

        // 標準報酬算入依頼ファイル情報DtoListの件数分、以下処理を実施する
        for (Cfh35RewardDataDto targetDto : list) {
            // （１）標準報酬算入依頼ファイル出力用オブジェクトのインスタンス生成
            CFH35F01Dto cfh35F01Dto = new CFH35F01Dto();

            // （２）標準報酬算入依頼ファイル出力用オブジェクトに設定
            // 控除年月
            cfh35F01Dto.setKojoym(targetDto.getKojoym());
            // 氏名コード
            cfh35F01Dto.setNameCd(targetDto.getNameCd());
            // 氏名
            cfh35F01Dto.setName(targetDto.getName());
            // 管一
            cfh35F01Dto.setKanitsuKbn(targetDto.getKanitsuKbn());
            // 給与支給組織名
            cfh35F01Dto.setKyuyoSikyusoshikiNm(targetDto.getKyuyoSikyusoshikiNm());
            // 社宅名
            cfh35F01Dto.setSytNm(targetDto.getSytNm());
            // 棟室番号
            cfh35F01Dto.setHyojiyoheyaNo(targetDto.getHyojiyoheyaNo());
            // 入居年月日
            if (targetDto.getNykDt() != null) {
                cfh35F01Dto.setNykDt(targetDto.getNykDt().format(DateTimeFormatter.ofPattern("yyyy/MM/dd")));
            }
            // 退居年月日
            if (targetDto.getTykDt() != null) {
                cfh35F01Dto.setTykDt(targetDto.getTykDt().format(DateTimeFormatter.ofPattern("yyyy/MM/dd")));
            }
            // 給与支給会社コード
            cfh35F01Dto.setKyuyoSikyuKaisyaCd(targetDto.getKyuyoSikyuKaisyaCd());
            // 給与支給会社名
            cfh35F01Dto.setKaishaseishikiNm(targetDto.getKaishaseishikiNm());
            // 勤務先都道府県
            cfh35F01Dto.setKinmusakitodoufukenCd(targetDto.getKinmusakitodoufukenCd());
            // 居住面積
            cfh35F01Dto.setKyojumenseki(String.valueOf(targetDto.getKyojumenseki()));
            // 都道府県単価
            cfh35F01Dto.setTodoufukenAmt(String.valueOf(targetDto.getTodoufukenAmt()));
            // 都道府県別の価額
            cfh35F01Dto.setTodoufukenBetsuAmt(String.valueOf(targetDto.getTodoufukenBetsuAmt()));
            // 社宅使用料（共益費除く）
            cfh35F01Dto.setStkAmtSubKykh(String.valueOf(targetDto.getStkAmtSubKykh()));
            // 標準報酬月額算入額
            cfh35F01Dto.setStdCompAmt(String.valueOf(targetDto.getStdCompAmt()));

            // （3）返却用オブジェクトリストに標準報酬算入依頼ファイル出力Dtoを設定する。
            retList.add(cfh35F01Dto);
        }

        // --２．社員単位処理--END

        // --３．返却処理--START

        // 標準報酬算入依頼ファイル出力DtoListを返却する。
        return retList;

        // --３．返却処理--END
    }

    /**
     * <p>[概 要] 標準報酬算入依頼ファイルオブジェクト作成処理</p>
     * <p>[詳 細] <br>
     * 標準報酬算入依頼ファイルオブジェクト作成処理を行う</p>
     * <p>[備 考] </p>
     *
     * @param outputDir 出力ディレクトリ
     * @param outputFName 出力ファイル名
     * @param mkDir 作成ディレクトリ項目名
     * @param fName 作成ファイル名（全社の場合は {@link #ALL_DIR}、会社別の場合は給与支給会社コード）
     * @return KoseiVariableItemWriter 標準報酬算入依頼ファイルオブジェクト
     * @throws KoseiBatchException ファイル作成エラーまたはヘッダ出力エラーが発生した場合
     */
    private KoseiVariableItemWriter<CFH35F01Dto, KoseiVariableDto.None,
            KoseiVariableDto.None, KoseiVariableDto.None> makeFile(
                    String outputDir, String outputFName, String mkDir, String fName) {

        // --1.一括ファイル、会社別ファイルの出力--START

        // (1)下記の処理により作成ファイルパス名を設定する。
        String fileName = "";
        switch (mkDir) {
            // ア．作成ディレクトリ項目名がALL_DIRの場合
            case ALL_DIR:
                fileName = this.cfBatchUtils.combinePath(outputDir, outputFName);
                break;

            // イ．作成ディレクトリ項目名がKAISHA_DIRの場合
            case KAISHA_DIR:
                String wkPath = this.cfBatchUtils.combinePath(outputDir, fName + "_/");
                // 出力ディレクトリを作成
                File wkDir = new File(wkPath);

                // 出力ディレクトリが存在しない場合は作成する
                if (!wkDir.exists()) {
                    wkDir.mkdir();
                }
                fileName = this.cfBatchUtils.combinePath(wkPath, fName + outputFName);
                break;
            default:
                break;
        }

        // (2)一括ファイル、会社別ファイル用KoseiVariableItemWriterを生成する
        KoseiVariableItemWriter<CFH35F01Dto, KoseiVariableDto.None,
                KoseiVariableDto.None, KoseiVariableDto.None> writer =
                new KoseiVariableItemWriter.Builder<>(CFH35F01FileLayout.class)
                        .filePath(fileName)
                        .shouldDeleteIfEmpty(false)
                        .build();
        try {
            writer.open();
        } catch (KoseiFileException e) {
            // ファイル作成エラーが発生した場合、例外を出力する
            throw new KoseiBatchException(KoseiBatchSSMessageId.SSB00E002, e, fileName);
        }
        try {
            // タイトル行（@KoseiVariableColumn#columnName）を書き込む
            writer.writeTitle(CFH35F01Dto.class);
        } catch (KoseiFileException e) {
            // ヘッダ出力エラーが発生した場合、例外を出力する
            KoseiFileUtils.closeQuietly(writer);
            throw new KoseiBatchException(KoseiBatchSSMessageId.SSB00E008, e, fileName);
        }

        // (3)一括ファイル、会社別ファイル用KoseiVariableItemWriterを返却する
        return writer;

        // --1.一括ファイル、会社別ファイルの出力--END
    }
}
