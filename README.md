            INSERT  INTO gym_shain_ippijoho(                 
            name_cd                                            
            , kojo_ym                                          
            , kana_name                                        
            , name                                             
            , soshiki_cd_honmu                                 
            , soshiki_honmu_nm                                 
            , kyuyo_sikyusoshiki_cd                            
            , kyuyo_sikyusoshiki_nm                            
            , sihkizoku_kaisha_cd                              
            , sihkizoku_kaisha_nm                              
            , kaisha_honmu_cd                                  
            , kaisha_honmu_nm                                  
            , syukkosaki_kaisha_cd                             
            , syukkosaki_kaisha_nm                             
            , kanitsu_kbn                                      
            , sihjotai_cd                                      
            , yukyumukyu_kbn                                   
            , sekyutaisyo_cd                                   
            , shokunoshikaku_nm                                
            , seibetsu_cd                                      
            , seinengappi                                      
            , age                                              
            , saiyou_dt                                        
            , taishoku_dt                                      
            , sytteinentotatsuysetai                           
            , syt_tanshinteinentotatsuy                        
            , zaishok_kbn_cd                                   
            , syukko_sbt_cd                                    
            , tanshinhunin_hyoji                               
            , kojoirai_amt                                     
            , kojoiraishohizei_uchiwake                        
            , chg_rekyuyo_soshiki_cd                           
            , chg_rekaisha_cd                                  
            , chg_reyukyumukyu_kbn                             
            , chg_reido_cd                                     
            , kojokekka                                        
            , chg_rezaishok_kbn_cd                             
            , chg_resyukko_sbt_cd                              
            , hyojiyosekyutaisyo_cd                            
            , mochiie_getjyoukyou_cd                           
            , mochiiesyonin_dt                                 
            , mochiiesyonin_sytmgrsyunin_cd                    
            , sih_kbn_cd                                       
            , kyuyo_sikyukaisha_cd                             
            , kyuyo_sikyubuka_cd                               
            , syukkomoto_kaisha_cd                             
            , syukkomoto_soshiki_cd                            
            , cafe_riyo_kaisha_cd                              
            , ido_cd                                           
            , ninme_dt                                         
            , syukkosaki_sih_kbn_cd                            
            , kyuyo_sikyusih_kbn_cd                            
            , kojo_taishogai_flg                               
            , create_dttm                                      
            , create_user_id                                   
            , last_update_dttm                                 
            , last_update_user_id                              
            , kyuyo_sikyu_kbn_cd                               
            , sih_shikibetsu                                   
            )   
            SELECT 
             LPAD((9790720 + emp - 1)::text,7,'0') AS name_cd,
             TO_CHAR(mon,'YYYY/MM') AS kojo_ym,
             'ｼﾒｲ ﾀﾞﾐｲ' || LPAD(emp::text,7,'0') AS kana_name,
             '氏名　田見井' || LPAD(emp::text,7,'0') AS name,        
            '81804001','クレジットカード事業本部　カード営業推進部','81001000','','9','NTTｸﾞﾙｰﾌﾟ各社','910001','ＮＴＴファイナンス株式会社','','','1','01','','0','JG／POG','1','1965/03/02',59,'1988/04/01',NULL,2009,1999,'15','0','0',0,0,' ',' ',' ',' ',' ',' ',' ','0','0',NULL,'','01','910001','511','910001','81804001','910001','000','2024/04/01','','01','0','2026-07-28 14:47:12.000','SEINO','2026-07-28 14:47:12.000','SEINO','1','2'                
             FROM generate_series(1,342027) emp
             CROSS JOIN generate_series(
               DATE '2000-01-01',
               DATE '2026-08-01',
               INTERVAL '1 month'
             
             )mon;
