package jp.co.nttcom.kosei.batch.ss.config.jobs;

import jp.co.nttcom.kosei.batch.cf.cfh35.CFH3501Tasklet;
import jp.co.nttcom.kosei.common.ss.batch.params.CFH3501JobParams;

import org.springframework.batch.core.Job;
import org.springframework.batch.core.Step;
import org.springframework.batch.core.job.builder.JobBuilder;
import org.springframework.batch.core.repository.JobRepository;
import org.springframework.batch.core.step.builder.StepBuilder;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.transaction.PlatformTransactionManager;

/**
 * <p>[概 要] CFH3501_標準報酬算入依頼ファイル作成 ジョブ定義</p>
 * <p>[詳 細] <br>
 * Terasolunaバッチ５系 Taskletモデル。
 * </p>
 * <p>[備 考] </p>
 * <p>[環 境] JRE 21</p>
 * <p>Copyright © NTT DOCOMO SOLUTIONS, Inc.</p>
 *
 * @version 1.0
 * @author ビジネストランスフォーメーション本部 コーポレートビジネスソリューション部
 */
@ComponentScan(basePackages = "jp.co.nttcom.kosei.batch.cf.cfh35")
public class CFH3501JobConfig extends KoseiJobConfig {

    /**
     * <p>[概 要] 「CFH3501」ステップ定義１</p>
     * <p>[詳 細] 標準報酬算入依頼ファイル作成ステップ</p>
     *
     * @param jobRepository ジョブリポジトリ
     * @param transactionManager トランザクションマネージャ
     * @param tasklet タスクレットオブジェクト
     * @return ステップオブジェクト
     */
    @Bean
    public Step cfh3501Step01(JobRepository jobRepository,
            @Qualifier("jobTransactionManager") PlatformTransactionManager transactionManager,
            CFH3501Tasklet tasklet) {
        return new StepBuilder(CFH3501JobParams.JOB_NAME + ".step01", jobRepository)
                .tasklet(tasklet, transactionManager)
                .listener(koseiStepExecutionListener)
                .build();
    }

    /**
     * <p>[概 要] 「CFH3501」ジョブ定義</p>
     * <p>[詳 細] 標準報酬算入依頼ファイル作成ジョブ</p>
     *
     * @param jobRepository ジョブリポジトリ
     * @param step01 ステップ１オブジェクト
     * @return ジョブオブジェクト
     */
    @Bean
    public Job cfh3501Job(JobRepository jobRepository,
            @Qualifier("cfh3501Step01") Step step01) {
        return new JobBuilder(CFH3501JobParams.JOB_NAME, jobRepository)
                .start(step01)
                .listener(koseiJobExecutionListener)
                .build();
    }
}
