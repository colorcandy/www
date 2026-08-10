SELECT
    s.name_cd,
    s.kojo_ym,
    s.kyuyo_sikyukaisha_cd,
    s.sekyutaisyo_cd,
    s.kojo_taishogai_flg,
    k.rec_sbt,
    k.sytteinen_tekiyo_kbn_cd
FROM public.gym_shain_ippijoho s
INNER JOIN public.gym_seikyu_kanri k
    ON s.kojo_ym = k.kojo_ym
   AND s.name_cd = k.name_cd
WHERE EXISTS (
    SELECT 1
    FROM public.gym_batch_soko_kanri b
    WHERE b.kyuyosetuzoku_sokom = s.kojo_ym
)
  AND k.rec_sbt = '0'
  AND k.sytteinen_tekiyo_kbn_cd = '0'
  AND s.sekyutaisyo_cd <> '3'
  AND s.kojo_taishogai_flg <> '1'
  AND s.kyuyo_sikyukaisha_cd IN ('A', 'B', 'C')
ORDER BY
    s.kyuyo_sikyukaisha_cd,
    s.name_cd;
