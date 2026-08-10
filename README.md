# カフェテリア

# 共通
cf.cafeminutes=120
cf.home.dir=${ss.ks_home.dir}
cf.download.dir=${cf.home.dir}/download
cf.onbatch.input.dir=${ss.download.dir}/gy/gyg01/on_batch/input
cf.result.list.output.dir=${cf.download.dir}/gy/gyg01/on_batch/output
cf.error.list.output.dir=${cf.download.dir}/gy/gyg01/on_batch/output
cf.transaction.limit.records=10

# CFB20 代行申請一括登録
CFB2001.name=代行申請一括登録処理

# CFB30 申請状況チェック
CFB3001.name=申請状況チェック処理
CFB3001.download.cfb30f04.1.dir=${cf.download.dir}/gy/gyg02/batch/output/cfb30f04-1/
CFB3001.download.cfb30f04.2.dir=${cf.download.dir}/gy/gyg02/batch/output/cfb30f04-2/
CFB3001.download.cfb30f04.3.dir=${cf.download.dir}/gy/gyg02/batch/output/cfb30f04-3/

CFB3001.download.cfb30f05.1.dir=${cf.download.dir}/gy/gyg02/batch/output/cfb30f05-1/
CFB3001.download.cfb30f05.2.dir=${cf.download.dir}/gy/gyg02/batch/output/cfb30f05-2/
CFB3001.download.cfb30f05.3.dir=${cf.download.dir}/gy/gyg02/batch/output/cfb30f05-3/

CFB3001.download.cfb30f06.1.dir=${cf.download.dir}/gy/gyg02/batch/output/cfb30f06-1/
CFB3001.download.cfb30f06.2.dir=${cf.download.dir}/gy/gyg02/batch/output/cfb30f06-2/
CFB3001.download.cfb30f06.3.dir=${cf.download.dir}/gy/gyg02/batch/output/cfb30f06-3/

CFB3001.data.cfb30.dir=${cf.home.dir}/data/cf/cfb30
CFB3001.data.url.jutakuhojotop=/ap/jh/JHA01S01/selfApplySet
CFB3001.data.url.jutakuloantop=/ap/jl/JLA01S01/selfApplySet
CFB3001.data.url.rishihokyudl=/top/f2/FRG02S01/search
CFB3001.data.url.ippanzaikei=/ap/nz/NZA02S01/
CFB3001.data.url.nenkinzaikei=/ap/nz/NZA02S01/
CFB3001.data.url.jutakuzaikei=/ap/nz/NZA02S01/
CFB3001.data.url.mochikabudl=/top/f2/FRG02S01/search

# CFC50 事業者前日申請削除
CFC5001.name=事業者前日申請削除処理

# CFK40 カフェ加入状況一括登録
CFK4001.name=カフェテリア加入状況一括登録処理
CFK4001.transaction.limit.records=${cf.transaction.limit.records}

#CFJ10 利子補給登録済情報取込
CFJ1001.name=利子補給登録済情報取込処理
CFJ1001.la.input.dir=${cf.home.dir}/ftp/usr/rcv/
CFJ1001.result.list.output.cfj10f04.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj10f04/
CFJ1001.result.list.output.cfj10f05.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj10f05/
CFJ1001.result.list.output.cfj10f06.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj10f06/
CFJ1001.error.list.output.cfj10f07.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj10f07/
CFJ1001.error.list.output.cfj10f08.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj10f08/
CFJ1001.error.list.output.cfj10f09.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj10f09/
CFJ1001.transaction.limit.records=${cf.transaction.limit.records}

#CFJ20 利子補給ポイント情報作成
CFJ2001.name=利子補給ポイント情報作成処理
CFJ2001.result.list.output.cfj20f01.dir=${cf.home.dir}/ftp/usr/snd/
CFJ2001.result.list.output.cfj20f02.dir=${cf.home.dir}/ftp/usr/snd/
CFJ2001.result.list.output.cfj20f03.dir=${cf.home.dir}/ftp/usr/snd/
CFJ2001.result.list.output.cfj20f04.dir=${cf.home.dir}/ftp/usr/snd/
CFJ2001.result.list.output.cfj20f05.dir=${cf.home.dir}/ftp/usr/snd/
CFJ2001.result.list.output.cfj20f06.dir=${cf.home.dir}/ftp/usr/snd/
CFJ2001.result.list.download.cfj20f01.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj20f01/
CFJ2001.result.list.download.cfj20f02.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj20f02/
CFJ2001.result.list.download.cfj20f03.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj20f03/
CFJ2001.result.list.download.cfj20f04.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj20f04/
CFJ2001.result.list.download.cfj20f05.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj20f05/
CFJ2001.result.list.download.cfj20f06.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj20f06/

#CFJ30 利子補給支給情報取込
CFJ3001.name=利子補給支給情報取込処理
CFJ3001.la.input.dir=${cf.home.dir}/ftp/usr/rcv/
CFJ3001.result.list.output.cfj30f04.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f04-1/
CFJ3001.result.list.output.cfj30f05.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f05-1/
CFJ3001.result.list.output.cfj30f06.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f06-1/
CFJ3001.result.list.output.cfj30f07.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f07-2/
CFJ3001.result.list.output.cfj30f08.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f08-2/
CFJ3001.result.list.output.cfj30f09.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f09-2/
CFJ3001.result.list.output.cfj30f10.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f10-3/
CFJ3001.result.list.output.cfj30f11.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f11-3/
CFJ3001.result.list.output.cfj30f12.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f12-3/
CFJ3001.result.list.output.cfj30f13.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f13/
CFJ3001.result.list.output.cfj30f14.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f14/
CFJ3001.result.list.output.cfj30f15.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f15/
CFJ3001.error.list.output.cfj30f16.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f16/
CFJ3001.error.list.output.cfj30f17.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f17/
CFJ3001.error.list.output.cfj30f18.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj30f18/

#CFJ40
CFJ4001.name=START拠出額状況取込
CFJ4001.start.input.file=NKAB00KST0801
CFJ4001.start.input.dir=${KS_HOME}/ftp/usr/rcv/
CFJ4001.result.list.output.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj40f02/
CFJ4001.error.list.output.dir=${cf.download.dir}/gy/gyg02/batch/output/cfj40f03/
CFJ4001.commit.limit.count=10000

#CFK30 年度更新
CFK3001.name=年度更新処理
CFK3001.la.input.dir=${cf.home.dir}/data/cf/cfk30/
CFK3001.result.list.output.cfk30f02.dir=${cf.download.dir}/gy/gyg02/batch/output/cfk30f02/
CFK3001.result.list.output.cfk30f03.dir=${cf.download.dir}/gy/gyg02/batch/output/cfk30f03/
CFK3001.error.list.output.cfk30f04.dir=${cf.download.dir}/gy/gyg02/batch/output/cfk30f04/
CFK3001.transaction.limit.records=${cf.transaction.limit.records}

#CFH10 統計データ抽出
CFH1001.name=統計データ抽出処理
CFH1001.download.cfh10f01.1.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh10f01-1/
CFH1001.download.cfh10f01.2.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh10f01-2/
CFH1001.download.cfh10f01.3.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh10f01-3/
CFH1001.download.cfh10f02.1.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh10f02-1/
CFH1001.download.cfh10f02.2.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh10f02-2/
CFH1001.download.cfh10f02.3.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh10f02-3/
CFH1001.download.cfh10f03.1.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh10f03-1/
CFH1001.download.cfh10f03.2.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh10f03-2/
CFH1001.download.cfh10f04.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh10f04/

#CFH20 課税データ作成
CFH2001.name=課税データ作成処理
CFH2001.download.cfh20f01.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh20f01/
CFH2001.download.cfh20f02.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh20f02/

#CFH33 課税処理依頼データ作成
CFH3301.name=課税処理依頼データ作成処理
CFH3301.output.cfh33f01.dir=${cf.home.dir}/ftp/usr/snd/
CFH3301.download.cfh33f01.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh33f01/
CFH3301.download.cfh33f02.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh33f02/
CFH3301.commit.limit.count=10000

# CFH35 標準報酬算入依頼ファイル作成
CFH3501.name=標準報酬算入依頼ファイル作成
CFH3501.download.cfh35f01.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh35f01/
CFH3501.download.cfh35f02.dir=${cf.download.dir}/gy/gyg02/batch/output/cfh35f02/

# CFG10 予約可能枠登録
CFG1001.name=予約可能枠登録処理

# CFG30 受検日登録
CFG3001.name=受検日登録処理

# CFI10 カフェテリアステータス情報一括登録
CFI1001.name=カフェテリアステータス情報一括登録処理

# CFI20 申請者情報取込
CFI2001.name=申請者情報取込
CFI2001.commit.limit.count=10000

# CFK50 ポイント自動繰越
CFK5001.name=ポイント自動繰越
CFK5001.download.cfk50f01.dir=${cf.download.dir}/gy/gyg02/batch/output/cfk50f01/
CFK5001.commit.limit.count=10000

# CFH34 標準報酬化対応アップロード
CFH3401.id=CFH34
CFH3401.name=標準報酬化対応アップロード
