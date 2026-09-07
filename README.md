<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="jp.co.nttcom.kosei.batch.nz.nzb01.NZB01Dao">

  <sql id="SELECT_COMMON">
        GSJ.NAME_CD,
        GSJ.SHANAI_NAME_KANJI,
        GSJ.KYUYO_SIKYUKAISHA_CD,
        GSJ.KYUYO_SIKYUSOSHIKI_CD,
        GSJ.OLDSIHKYUYO_SIKYUKAISHA_CD,
        GSJ.OLDSIHKYUYO_SIKYUSOSHIKI_CD,
        GSJ.KYUSHOKU_KAISHI_DT,
        GSJ.SYUKKO_DT,
        GSJ.SYUKKO_SBT_CD,
        GSJ.TAISHOKU_DT,
        GSJ.TAISHOKURIYU_CD,
        GSJ.KAIGAIKINMUNADOSYUKKO_KAISI_YM,
        GSJ.KYUSYOKU_RIYU_CD,
        GSI.SYUKKOMOTO_KAISHA_CD,
        NK.ZAIKEI_SBT_CD,
        NK.KEIYAKUKIN_CD,
        NK.CHUDAN_S_YM,
        NK.SAIKAI_YM,
        NK.NZK_TSUMI_E_YM,
        NK.MONTSUMI_AMT,
        NK.SUMTSUMI_AMT,
        NK.WIN_TSUMI_AMT,
        NK.HIKAZEILIM_AMT,
        NK.CHOHYORET_YM,
        NK.CHOHYOSHUKKO_YM,
        NK.KAIYAKU_YM,
        COALESCE(NSM1.SOSHIKI_NM, ' ') AS "jigyoshomei",
        COALESCE(NSM2.SOSHIKI_NM, ' ') AS "oldJigyoshomei",
        COALESCE(GK.KINYUKIKANKANA_NM, ' ') AS "kinyukikankanaNm",
        COALESCE(GK.KINYUKIKANKANJI_NM, ' ') AS "kinyukikankanjiNm",
        COALESCE(NKM1.ZAIKE_KINYUKIKSETUZK_KAISHA_CD, '99999') AS "zaikeKinyukiksetuzkKaishaCd",
        COALESCE(NKM2.ZAIKE_KYODORIYOKAISHA_KBN_CD, '0') AS "zaikeKyodoriyokaishaKbnCd"
  </sql>

  <sql id="FROM_COMMON">
    GYM_SHAIN_JOHO GSJ
    INNER JOIN GYM_SHAIN_IPPIJOHO GSI
        ON GSJ.NAME_CD = GSI.NAME_CD
        AND GSI.KOJO_YM =  '' || #{toMonth} / 100 ||'/' || lpad('' || #{toMonth} % 100, 2, '0')
    INNER JOIN NZM_KEIYAKU NK
        ON GSJ.NAME_CD = NK.NAME_CD
    LEFT JOIN GYM_KEIYAKUKINYU GK
        ON NK.KEIYAKUKIN_CD = GK.KINYUKIKAN_CD
    LEFT JOIN NZV_SOSHIKI_MASTER NSM1
        ON GSJ.KYUYO_SIKYUSOSHIKI_CD = NSM1.SOSHIKI_CD
        AND GSJ.KYUYO_SIKYUKAISHA_CD = NSM1.KAISHA_CD
    LEFT JOIN NZV_SOSHIKI_MASTER NSM2
        ON GSJ.OLDSIHKYUYO_SIKYUSOSHIKI_CD = NSM2.SOSHIKI_CD
        AND GSJ.OLDSIHKYUYO_SIKYUKAISHA_CD = NSM2.KAISHA_CD
    LEFT JOIN NZV_KAISHA_MASTER NKM1
        ON GSJ.KYUYO_SIKYUKAISHA_CD = NKM1.KAISHA_CD
    LEFT JOIN NZV_KAISHA_MASTER NKM2
        ON GSJ.SYUKKOSAKI_KAISHA_CD = NKM2.KAISHA_CD
  </sql>

  <select id="selectIdoKbnCd1" resultType="jp.co.nttcom.kosei.batch.nz.nzb01.NZB01OutputDto">
    SELECT
        <include refid="SELECT_COMMON" />
    FROM
        <include refid="FROM_COMMON" />
     <![CDATA[
    WHERE 
    (   (   (
            GSJ.TAISHOKU_DT >= #{zenMonth2}
            AND GSJ.TAISHOKU_DT <= #{toMonth1}
              )
            OR
            (
            GSJ.TAISHOKU_DT >= #{zzenMonth2}
            AND GSJ.TAISHOKU_DT <= #{zenMonth1}
            AND NK.CHOHYORET_YM <> #{zenMonth}
            )
        )
        AND NK.KEIYAKUJOTAI_CD NOT IN ('09', '19')
    )
    OR
    (
    GSJ.TAISHOKU_DT IS NOT NULL
    AND NK.KAIYAKU_YM = #{toMonth}
    )
    ORDER BY GSJ.NAME_CD,
        NK.ZAIKEI_SBT_CD
    ]]>
  </select>

  <select id="selectIdoKbnCd2" resultType="jp.co.nttcom.kosei.batch.nz.nzb01.NZB01OutputDto">
    SELECT
        <include refid="SELECT_COMMON" />
    FROM
        <include refid="FROM_COMMON" />
     <![CDATA[
    WHERE 
    (   (   (
            GSJ.SYUKKO_DT >= #{zenMonth2}
            AND GSJ.SYUKKO_DT <= #{toMonth1}
            )
            OR
            (
            GSJ.SYUKKO_DT >= #{zzenMonth2}
            AND GSJ.SYUKKO_DT <= #{zenMonth1}
            AND NK.CHOHYORET_YM <> #{zenMonth}
            )
        )
        AND
        (
        GSJ.SYUKKO_HUKKI_DT IS NULL
        OR GSJ.SYUKKO_HUKKI_DT > #{toMonth1}
        )
    AND GSJ.SYUKKO_SBT_CD = '4'
    AND NK.KEIYAKUJOTAI_CD NOT IN ('09','19')
    )
    ORDER BY GSJ.NAME_CD,
        NK.ZAIKEI_SBT_CD
    ]]>
  </select>

  <select id="selectIdoKbnCd3" resultType="jp.co.nttcom.kosei.batch.nz.nzb01.NZB01OutputDto">
    SELECT
        <include refid="SELECT_COMMON" />
    FROM
        <include refid="FROM_COMMON" />
     <![CDATA[
    WHERE
    (   (   (
            GSJ.KYUSHOKU_KAISHI_DT >= #{zenMonth2}
            AND GSJ.KYUSHOKU_KAISHI_DT <= #{toMonth1}
            )
            OR
            (
            GSJ.KYUSHOKU_KAISHI_DT >= #{zzenMonth2}
            AND GSJ.KYUSHOKU_KAISHI_DT <= #{zenMonth1}
            AND NK.CHOHYOSHUKKO_YM <> #{zenMonth}
            )
        )
        AND 
        (
        GSJ.HKSYK_DT IS NULL
        OR GSJ.HKSYK_DT > #{toMonth1}
        )
    AND GSJ.KYUSYOKU_RIYU_CD = '05'
    AND NK.KEIYAKUJOTAI_CD NOT IN ('09','19')
    )
    ORDER BY GSJ.NAME_CD,
        NK.ZAIKEI_SBT_CD
    ]]>
  </select>

  <select id="selectIdoKbnCd4" resultType="jp.co.nttcom.kosei.batch.nz.nzb01.NZB01OutputDto">
    SELECT
        <include refid="SELECT_COMMON" />
    FROM
        <include refid="FROM_COMMON" />
     <![CDATA[
    WHERE 
    (   (   (
            GSJ.SYUKKO_DT >= #{zenMonth2}
            AND GSJ.SYUKKO_DT <= #{toMonth1}
            )
            OR
            (
            GSJ.SYUKKO_DT >= #{zzenMonth2}
            AND GSJ.SYUKKO_DT <= #{zenMonth1}
            AND NK.CHOHYOSHUKKO_YM <> #{zenMonth}
            )
        )
        AND 
        (
        GSJ.SYUKKO_HUKKI_DT IS NULL
        OR GSJ.SYUKKO_HUKKI_DT > #{toMonth1}
        )
    AND GSJ.SYUKKO_SBT_CD = '2'
    AND NK.KEIYAKUJOTAI_CD NOT IN ('09','19')
    )
    ORDER BY GSJ.NAME_CD,
        NK.ZAIKEI_SBT_CD
    ]]>
  </select>

</mapper>
