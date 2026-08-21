WITH base AS (
    SELECT
        s.name_cd,
        s.name,
        s.kojo_ym,
        s.sihjotai_cd,             -- 社员状态
        s.syukko_sbt_cd,           -- 出向种别
        s.hyojiyosekyutaisyo_cd,   -- 表示用请款对象
        s.kojoirai_amt,            -- 控除依赖额
        s.yukyumukyu_kbn,
        s.kojokekka,
        k.mgr_no,
        k.rec_sbt,
        k.kyuyosys_sbt_cd,         -- 02 SPEAD / 03 COM人给
        k.syt_amt,
        k.syt_konkaisesan_amt,
        k.tyusya_amt,
        k.tyusyakonkaisesangaku,
        k.sytteinen_tekiyo_kbn_cd
    FROM public.gym_shain_ippijoho s
    JOIN public.gym_seikyu_kanri k
      ON k.name_cd = s.name_cd
     AND k.kojo_ym = s.kojo_ym
    WHERE s.kojo_ym = '202608'
      AND k.kyuyosys_sbt_cd IN ('02', '03')
      AND COALESCE(s.kojoirai_amt, 0) <> 0
)
SELECT *
FROM base
ORDER BY sihjotai_cd, syukko_sbt_cd, name_cd, rec_sbt;


-- PTN-001：社員 + 输入文件控除结果 51
-- 预期：社宅使用料控除完了者一覧
SELECT *
FROM base
WHERE sihjotai_cd = '01'
  AND hyojiyosekyutaisyo_cd = '0';

  -- PTN-002：在職出向 + 输入文件控除结果 61
-- 预期：控除エラーリスト + 控除完了者一覧
SELECT *
FROM base
WHERE sihjotai_cd = '02'
  AND hyojiyosekyutaisyo_cd = '0';
  -- PTN-003：在籍出向（非在A/C）+ 输入文件控除结果 71
-- 预期：控除エラーリスト + 個人宛請求明細表
SELECT *
FROM base
WHERE sihjotai_cd = '03'
  AND COALESCE(syukko_sbt_cd, '') <> '2'
  AND hyojiyosekyutaisyo_cd = '0';

  -- PTN-004：キャリアスタッフ + 输入文件控除结果 71
-- 预期：控除エラーリスト + 個人宛請求明細表
SELECT *
FROM base
WHERE sihjotai_cd = '09'
  AND hyojiyosekyutaisyo_cd = '0';


  SELECT
    s.name_cd,
    s.name,
    s.sihjotai_cd,
    s.syukko_sbt_cd,
    s.kojokekka,
    s.hyojiyosekyutaisyo_cd,
    k.mgr_no,
    k.rec_sbt,
    k.kojo_errnaiyo,
    k.sekyuriyu_cd,
    CASE
        WHEN s.kojokekka IN ('51', '61')
         AND s.sihjotai_cd NOT IN ('04', '05', '08', '11', '12')
        THEN '完成者一覧対象'

        WHEN s.kojokekka = '61'
        THEN 'エラーリスト対象（金额修改）'

        WHEN s.kojokekka IN ('71', '84', '83')
         AND NOT (
             s.sihjotai_cd = '03'
             AND s.syukko_sbt_cd = '2'
             AND s.kojokekka IN ('71', '84')
         )
        THEN 'エラーリスト対象'

        ELSE '対象外／要确认'
    END AS expected_result
FROM public.gym_shain_ippijoho s
JOIN public.gym_seikyu_kanri k
  ON k.name_cd = s.name_cd
 AND k.kojo_ym = s.kojo_ym
WHERE s.kojo_ym = '202608'
ORDER BY s.name_cd, k.rec_sbt;
