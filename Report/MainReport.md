# All Reports

## 1. ET Newsletter Performance Report

### About: This report is used to analyze the performance of each newsletter over each month in the last quarter 
as well as on a weekly level and last three to four days.

### Query-1:
~~~
select 
  * 
except 
  (properties), 
  case when properties = 'mail_sent' then 'Sent' when properties = 'mail_open' then 'Open' when properties = 'mail_click' then 'Click' when properties = 'users' then 'Users' when properties = 'open_percent' then 'Open Rate' when properties = 'click_open_percent' then 'Click To Open Rate' end as properties, 
  case when properties = 'mail_sent' then 1 when properties = 'mail_open' then 2 when properties = 'mail_click' then 3 when properties = 'users' then 4 when properties = 'open_percent' then 5 when properties = 'click_open_percent' then 6 end as prop_id 
from 
  (
    select 
      * 
    from 
      (
        select 
          nl_name, 
          mon, 
          val, 
          properties 
        from 
          (
            select 
              *, 
              case when mail_sent = 0 then 0 else round(
                cast(mail_open as float64)/ cast(mail_sent as float64)* 100, 
                2
              ) end as open_percent, 
              case when mail_open = 0 then 0 else round(
                cast(mail_click as float64)/ cast(mail_open as float64)* 100, 
                2
              ) end as click_open_percent 
            from 
              (
                select 
                  a.nl_name, 
                  case when c_date = current_date - 2 then 'D2' when c_date = current_date - 3 then 'D3' when c_date = current_date - 4 then 'D4' end as mon, 
                  sum(mail_sent) as mail_sent, 
                  sum(mail_open) as mail_open, 
                  sum(mail_click) as mail_click, 
                  sum(users) as users 
                from 
                  (
                    SELECT 
                      cast(
                        SUBSTR(
                          CAST(
                            datetime(__time, 'Asia/Kolkata') AS string
                          ), 
                          1, 
                          10
                        ) as Date
                      ) as c_date, 
                      case when nl_name = 'News Digest' 
                      and tags in (
                        'rejected_new_mailer', 'Rejected'
                      ) then 'News Digest Static' when nl_name = 'News Digest' 
                      and tags not in (
                        'rejected_new_mailer', 'Rejected'
                      ) then 'News Digest ML Generated' when nl_name = 'ET RISE Biz Listings' then 'ET RISE' else nl_name end as nl_name, 
                      nl_schedule_id, 
                      count(*) as mail_sent 
                    FROM 
                      `et-poc-042021.all_user.EtClientEvents` 
                    WHERE 
                      __time >= cast(
                        DATETIME_ADD(
                          DATETIME_ADD(
                            cast(current_date - 5 as datetime), 
                            INTERVAL 18 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        DATETIME_ADD(
                          DATETIME_ADD(
                            cast(current_date - 2 as datetime), 
                            INTERVAL 18 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and client_source = 'et_newsletter' 
                      and event_name in ('mail_sent') 
                      and nl_name not like '%Test%' 
                      and (
                        mailer_type in ('NEWSLETTER') 
                        or nl_name in ('News Digest')
                      ) 
                      and nl_name not in (
                        'ET Prime Promotions 2', 'ET Tech Promotional', 
                        'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
                        'ETtech Promotional Mailer', 'DeCrypt', 
                        'ETtech News Alert', 'On Board to ET CSV Mailer', 
                        'ET SmallBiz FMC', "ETPrime Today's Edition", 
                        'ET Promotions', 'ET Rise SME', 
                        'ET SmallBiz LUB', 'Unsubscription Mail', 
                        'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
                        'Coronavirus Newsletter', 'ET Specials', 
                        'ET Specials (ET Engaged Users)', 
                        'ET Specials : (ALL ET Audience)', 
                        'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
                      ) 
                    GROUP BY 
                      1, 
                      2, 
                      3
                  ) a 
                  left join (
                    select 
                      nl_name, 
                      nl_schedule_id, 
                      sum(mail_open) as mail_open, 
                      sum(mail_click) as mail_click, 
                      count(*) as users 
                    from 
                      (
                        SELECT 
                          case when nl_name = 'News Digest' 
                          and tags in (
                            'rejected_new_mailer', 'Rejected'
                          ) then 'News Digest Static' when nl_name = 'News Digest' 
                          and tags not in (
                            'rejected_new_mailer', 'Rejected'
                          ) then 'News Digest ML Generated' when nl_name = 'ET RISE Biz Listings' then 'ET RISE' else nl_name end as nl_name, 
                          email, 
                          nl_schedule_id, 
                          sum(
                            case when event_name = 'mail_open' then 1 else 0 end
                          ) as mail_open, 
                          sum(
                            case when event_name = 'mail_click' then 1 else 0 end
                          ) as mail_click 
                        FROM 
                          `et-poc-042021.all_user.EtClientEvents` 
                        WHERE 
                          __time > cast(
                            DATETIME_ADD(
                              DATETIME_ADD(
                                cast(current_date - 5 as datetime), 
                                INTERVAL 18 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and client_source = 'et_newsletter' 
                          and event_name in ('mail_open', 'mail_click') 
                          and nl_name not like '%Test%' 
                          and (
                            mailer_type in ('NEWSLETTER') 
                            or nl_name in ('News Digest')
                          ) 
                          and nl_name not in (
                            'ET Prime Promotions 2', 'ET Tech Promotional', 
                            'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
                            'ETtech Promotional Mailer', 'DeCrypt', 
                            'ETtech News Alert', 'On Board to ET CSV Mailer', 
                            'ET SmallBiz FMC', "ETPrime Today's Edition", 
                            'ET Promotions', 'ET Rise SME', 
                            'ET SmallBiz LUB', 'Unsubscription Mail', 
                            'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
                            'Coronavirus Newsletter', 'ET Specials', 
                            'ET Specials (ET Engaged Users)', 
                            'ET Specials : (ALL ET Audience)', 
                            'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
                          ) 
                        GROUP BY 
                          1, 
                          2, 
                          3
                      ) 
                    group by 
                      1, 
                      2
                  ) b on a.nl_schedule_id = b.nl_schedule_id 
                  and cast(a.nl_name as string)= cast(b.nl_name as string) 
                group by 
                  1, 
                  2
              ) 
            where 
              mail_sent > 5 
            union all 
            select 
              nl_name, 
              'last_7_days' as mon, 
              cast(
                round(
                  cast(mail_sent as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as mail_sent, 
              cast(
                round(
                  cast(mail_open as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as mail_open, 
              cast(
                round(
                  cast(mail_click as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as mail_click, 
              cast(
                round(
                  cast(users as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as users, 
              case when mail_sent = 0 then 0 else round(
                cast(mail_open as float64)/ cast(mail_sent as float64)* 100, 
                2
              ) end as open_percent, 
              case when mail_open = 0 then 0 else round(
                cast(mail_click as float64)/ cast(mail_open as float64)* 100, 
                2
              ) end as click_open_percent 
            from 
              (
                select 
                  nl_name, 
                  sum(mail_sent) as mail_sent, 
                  sum(mail_open) as mail_open, 
                  sum(mail_click) as mail_click, 
                  sum(users) as users, 
                  sum(day) as day 
                from 
                  (
                    select 
                      nl_name, 
                      case when mail_sent != 0 then 1 else 0 end as day, 
                      mail_sent, 
                      mail_open, 
                      mail_click, 
                      users 
                    from 
                      (
                        select 
                          c_date, 
                          nl_name, 
                          sum(mail_sent) as mail_sent, 
                          sum(mail_open) as mail_open, 
                          sum(mail_click) as mail_click, 
                          sum(
                            case when mail_open != 0 
                            or mail_click != 0 then 1 else 0 end
                          ) as users 
                        from 
                          (
                            SELECT 
                              cast(
                                SUBSTR(
                                  CAST(
                                    datetime(__time, 'Asia/Kolkata') AS string
                                  ), 
                                  1, 
                                  10
                                ) as Date
                              ) as c_date, 
                              case when nl_name = 'News Digest' 
                              and tags in (
                                'rejected_new_mailer', 'Rejected'
                              ) then 'News Digest Static' when nl_name = 'News Digest' 
                              and tags not in (
                                'rejected_new_mailer', 'Rejected'
                              ) then 'News Digest ML Generated' when nl_name = 'ET RISE Biz Listings' then 'ET RISE' else nl_name end as nl_name, 
                              email, 
                              sum(
                                case when event_name = 'mail_sent' then 1 else 0 end
                              ) as mail_sent, 
                              sum(
                                case when event_name = 'mail_open' then 1 else 0 end
                              ) as mail_open, 
                              sum(
                                case when event_name = 'mail_click' then 1 else 0 end
                              ) as mail_click 
                            FROM 
                              `et-poc-042021.all_user.EtClientEvents` 
                            WHERE 
                              __time >= cast(current_date - 7 as timestamp) 
                              and client_source = 'et_newsletter' 
                              and event_name in (
                                'mail_sent', 'mail_open', 'mail_click'
                              ) 
                              and nl_name not like '%Test%' 
                              and (
                                mailer_type in ('NEWSLETTER') 
                                or nl_name in ('News Digest')
                              ) 
                              and nl_name not in (
                                'ET Prime Promotions 2', 'ET Tech Promotional', 
                                'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
                                'ETtech Promotional Mailer', 'DeCrypt', 
                                'ETtech News Alert', 'On Board to ET CSV Mailer', 
                                'ET SmallBiz FMC', "ETPrime Today's Edition", 
                                'ET Promotions', 'ET Rise SME', 
                                'ET SmallBiz LUB', 'Unsubscription Mail', 
                                'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
                                'Coronavirus Newsletter', 'ET Specials', 
                                'ET Specials (ET Engaged Users)', 
                                'ET Specials : (ALL ET Audience)', 
                                'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
                              ) 
                            GROUP BY 
                              1, 
                              2, 
                              3
                          ) 
                        group by 
                          1, 
                          2
                      )
                  ) 
                group by 
                  1
              ) 
            where 
              mail_sent != 0 
            union all 
            select 
              a.nl_name, 
              a.mon, 
              case when day = 0 then 0 else cast(
                round(
                  cast(mail_sent as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) end as mail_sent, 
              case when day = 0 then 0 else cast(
                round(
                  cast(mail_open as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) end as mail_open, 
              case when day = 0 then 0 else cast(
                round(
                  cast(mail_click as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) end as mail_click, 
              case when day = 0 then 0 else cast(
                round(
                  cast(users as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) end as users, 
              case when mail_sent = 0 then 0 else round(
                cast(mail_open as float64)/ cast(mail_sent as float64)* 100, 
                2
              ) end as open_percent, 
              case when mail_sent = 0 then 0 else round(
                cast(mail_click as float64)/ cast(mail_open as float64)* 100, 
                2
              ) end as click_open_percent 
            from 
              (
                select 
                  nl_name, 
                  case when mon = extract(
                    month 
                    from 
                      DATE_SUB(
                        Date(
                          extract(
                            year 
                            from 
                              current_date()
                          ), 
                          extract(
                            month 
                            from 
                              current_date()
                          ), 
                          01
                        ), 
                        INTERVAL 1 MONTH
                      )
                  ) then 'M1' when mon = extract(
                    month 
                    from 
                      DATE_SUB(
                        Date(
                          extract(
                            year 
                            from 
                              current_date()
                          ), 
                          extract(
                            month 
                            from 
                              current_date()
                          ), 
                          01
                        ), 
                        INTERVAL 2 MONTH
                      )
                  ) then 'M2' when mon = extract(
                    month 
                    from 
                      DATE_SUB(
                        Date(
                          extract(
                            year 
                            from 
                              current_date()
                          ), 
                          extract(
                            month 
                            from 
                              current_date()
                          ), 
                          01
                        ), 
                        INTERVAL 3 MONTH
                      )
                  ) then 'M3' when mon = extract(
                    month 
                    from 
                      current_date()
                  ) then 'MTD' end as mon, 
                  sum(mail_sent) as mail_sent, 
                  sum(mail_open) as mail_open, 
                  sum(mail_click) as mail_click, 
                  sum(users) as users, 
                  sum(day) as day 
                from 
                  (
                    select 
                      nl_name, 
                      mon, 
                      case when mail_sent != 0 then 1 else 0 end as day, 
                      mail_sent, 
                      mail_open, 
                      mail_click, 
                      users 
                    from 
                      (
                        select 
                          nl_name, 
                          mon, 
                          c_date, 
                          sum(mail_sent) as mail_sent, 
                          sum(mail_open) as mail_open, 
                          sum(mail_click) as mail_click, 
                          sum(
                            case when mail_open != 0 
                            or mail_click != 0 then 1 else 0 end
                          ) as users 
                        from 
                          (
                            SELECT 
                              extract(
                                month 
                                from 
                                  cast(
                                    SUBSTR(
                                      CAST(
                                        datetime(__time, 'Asia/Kolkata') AS string
                                      ), 
                                      1, 
                                      10
                                    ) as Date
                                  )
                              ) as mon, 
                              cast(
                                SUBSTR(
                                  CAST(
                                    datetime(__time, 'Asia/Kolkata') AS string
                                  ), 
                                  1, 
                                  10
                                ) as Date
                              ) as c_date, 
                              case when nl_name = 'News Digest' 
                              and tags in (
                                'rejected_new_mailer', 'Rejected'
                              ) then 'News Digest Static' when nl_name = 'News Digest' 
                              and tags not in (
                                'rejected_new_mailer', 'Rejected'
                              ) then 'News Digest ML Generated' when nl_name = 'ET RISE Biz Listings' then 'ET RISE' else nl_name end as nl_name, 
                              email, 
                              sum(
                                case when event_name = 'mail_sent' then 1 else 0 end
                              ) as mail_sent, 
                              sum(
                                case when event_name = 'mail_open' then 1 else 0 end
                              ) as mail_open, 
                              sum(
                                case when event_name = 'mail_click' then 1 else 0 end
                              ) as mail_click 
                            FROM 
                              `et-poc-042021.all_user.EtClientEvents` 
                            WHERE 
                              __time >= cast (
                                DATE_SUB(
                                  Date(
                                    extract(
                                      year 
                                      from 
                                        current_date()
                                    ), 
                                    extract(
                                      month 
                                      from 
                                        current_date()
                                    ), 
                                    01
                                  ), 
                                  INTERVAL 3 MONTH
                                ) as timestamp
                              ) 
                              and client_source = 'et_newsletter' 
                              and event_name in (
                                'mail_sent', 'mail_open', 'mail_click'
                              ) 
                              and nl_name not like '%Test%' 
                              and (
                                mailer_type in ('NEWSLETTER') 
                                or nl_name in ('News Digest')
                              ) 
                              and nl_name not in (
                                'ET Prime Promotions 2', 'ET Tech Promotional', 
                                'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
                                'ETtech Promotional Mailer', 'DeCrypt', 
                                'ETtech News Alert', 'On Board to ET CSV Mailer', 
                                'ET SmallBiz FMC', "ETPrime Today's Edition", 
                                'ET Promotions', 'ET Rise SME', 
                                'ET SmallBiz LUB', 'Unsubscription Mail', 
                                'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
                                'Coronavirus Newsletter', 'ET Specials', 
                                'ET Specials (ET Engaged Users)', 
                                'ET Specials : (ALL ET Audience)', 
                                'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
                              ) 
                            GROUP BY 
                              1, 
                              2, 
                              3, 
                              4
                          ) 
                        group by 
                          1, 
                          2, 
                          3
                      )
                  ) 
                group by 
                  1, 
                  2
              ) a
          ) unpivot(
            val for properties in (
              mail_sent, mail_open, mail_click, 
              users, open_percent, click_open_percent
            )
          ) as upv
      ) pivot (
        sum(val) for mon in (
          'D2', 'D3', 'D4', 'last_7_days', 'MTD', 
          'M1', 'M2', 'M3'
        )
      )
  )
~~~ 

### Query-2:
~~~
select 
  *, 
  case when properties = 'mail_sent' then 1 when properties = 'mail_open' then 2 when properties = 'mail_click' then 3 when properties = 'users' then 4 when properties = 'open_percent' then 5 when properties = 'click_open_percent' then 6 end as prop_id 
from 
  (
    select 
      * 
    from 
      (
        select 
          mon, 
          val, 
          properties 
        from 
          (
            select 
              *, 
              case when mail_sent = 0 then 0 else round(
                cast(mail_open as float64)/ cast(mail_sent as float64)* 100, 
                2
              ) end as open_percent, 
              case when mail_open = 0 then 0 else round(
                cast(mail_click as float64)/ cast(mail_open as float64)* 100, 
                2
              ) end as click_open_percent 
            from 
              (
                select 
                  case when c_date = current_date - 2 then 'D2' when c_date = current_date - 3 then 'D3' when c_date = current_date - 4 then 'D4' end as mon, 
                  sum(mail_sent) as mail_sent, 
                  sum(mail_open) as mail_open, 
                  sum(mail_click) as mail_click, 
                  sum(users) as users 
                from 
                  (
                    SELECT 
                      cast(
                        SUBSTR(
                          CAST(
                            datetime(__time, 'Asia/Kolkata') AS string
                          ), 
                          1, 
                          10
                        ) as Date
                      ) as c_date, 
                      case when nl_name = 'News Digest' 
                      and tags in (
                        'rejected_new_mailer', 'Rejected'
                      ) then 'News Digest Static' when nl_name = 'News Digest' 
                      and tags not in (
                        'rejected_new_mailer', 'Rejected'
                      ) then 'News Digest ML Generated' else nl_name end as nl_name, 
                      nl_schedule_id, 
                      count(*) as mail_sent 
                    FROM 
                      `et-poc-042021.all_user.EtClientEvents` 
                    WHERE 
                      __time >= cast(
                        DATETIME_ADD(
                          DATETIME_ADD(
                            cast(current_date - 5 as datetime), 
                            INTERVAL 18 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        DATETIME_ADD(
                          DATETIME_ADD(
                            cast(current_date - 2 as datetime), 
                            INTERVAL 18 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and client_source = 'et_newsletter' 
                      and event_name in ('mail_sent') 
                      and nl_name not like '%Test%' 
                      and (
                        mailer_type in ('NEWSLETTER') 
                        or nl_name in ('News Digest')
                      ) 
                      and nl_name not in (
                        'ET Prime Promotions 2', 'ET Tech Promotional', 
                        'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
                        'ETtech Promotional Mailer', 'DeCrypt', 
                        'ETtech News Alert', 'On Board to ET CSV Mailer', 
                        'ET SmallBiz FMC', "ETPrime Today's Edition", 
                        'ET Promotions', 'ET Rise SME', 
                        'ET SmallBiz LUB', 'Unsubscription Mail', 
                        'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
                        'Coronavirus Newsletter', 'ET Specials', 
                        'ET Specials (ET Engaged Users)', 
                        'ET Specials : (ALL ET Audience)', 
                        'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
                      ) 
                    GROUP BY 
                      1, 
                      2, 
                      3
                  ) a 
                  left join (
                    select 
                      nl_schedule_id, 
                      sum(mail_open) as mail_open, 
                      sum(mail_click) as mail_click, 
                      count(*) as users 
                    from 
                      (
                        SELECT 
                          case when nl_name = 'News Digest' 
                          and tags in (
                            'rejected_new_mailer', 'Rejected'
                          ) then 'News Digest Static' when nl_name = 'News Digest' 
                          and tags not in (
                            'rejected_new_mailer', 'Rejected'
                          ) then 'News Digest ML Generated' else nl_name end as nl_name, 
                          email, 
                          nl_schedule_id, 
                          sum(
                            case when event_name = 'mail_open' then 1 else 0 end
                          ) as mail_open, 
                          sum(
                            case when event_name = 'mail_click' then 1 else 0 end
                          ) as mail_click 
                        FROM 
                          `et-poc-042021.all_user.EtClientEvents` 
                        WHERE 
                          __time >= cast(current_date - 5 as timestamp) 
                          and client_source = 'et_newsletter' 
                          and event_name in ('mail_open', 'mail_click') 
                          and nl_name not like '%Test%' 
                          and (
                            mailer_type in ('NEWSLETTER') 
                            or nl_name in ('News Digest')
                          ) 
                          and nl_name not in (
                            'ET Prime Promotions 2', 'ET Tech Promotional', 
                            'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
                            'ETtech Promotional Mailer', 'DeCrypt', 
                            'ETtech News Alert', 'On Board to ET CSV Mailer', 
                            'ET SmallBiz FMC', "ETPrime Today's Edition", 
                            'ET Promotions', 'ET Rise SME', 
                            'ET SmallBiz LUB', 'Unsubscription Mail', 
                            'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
                            'Coronavirus Newsletter', 'ET Specials', 
                            'ET Specials (ET Engaged Users)', 
                            'ET Specials : (ALL ET Audience)', 
                            'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
                          ) 
                        GROUP BY 
                          1, 
                          2, 
                          3
                      ) 
                    group by 
                      1
                  ) b on a.nl_schedule_id = b.nl_schedule_id 
                group by 
                  1
              ) 
            where 
              mail_sent > 5 
            union all 
            select 
              'last_7_days' as mon, 
              cast(
                round(
                  cast(mail_sent as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as mail_sent, 
              cast(
                round(
                  cast(mail_open as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as mail_open, 
              cast(
                round(
                  cast(mail_click as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as mail_click, 
              cast(
                round(
                  cast(users as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as users, 
              case when mail_sent = 0 then 0 else round(
                cast(mail_open as float64)/ cast(mail_sent as float64)* 100, 
                2
              ) end as open_percent, 
              case when mail_open = 0 then 0 else round(
                cast(mail_click as float64)/ cast(mail_open as float64)* 100, 
                2
              ) end as click_open_percent 
            from 
              (
                select 
                  sum(mail_sent) as mail_sent, 
                  sum(mail_open) as mail_open, 
                  sum(mail_click) as mail_click, 
                  sum(users) as users, 
                  count(*) as day 
                from 
                  (
                    select 
                      c_date, 
                      sum(mail_sent) as mail_sent, 
                      sum(mail_open) as mail_open, 
                      sum(mail_click) as mail_click, 
                      sum(
                        case when mail_open != 0 
                        or mail_click != 0 then 1 else 0 end
                      ) as users 
                    from 
                      (
                        SELECT 
                          cast(
                            SUBSTR(
                              CAST(
                                datetime(__time, 'Asia/Kolkata') AS string
                              ), 
                              1, 
                              10
                            ) as Date
                          ) as c_date, 
                          case when nl_name = 'News Digest' 
                          and tags in (
                            'rejected_new_mailer', 'Rejected'
                          ) then 'News Digest Static' when nl_name = 'News Digest' 
                          and tags not in (
                            'rejected_new_mailer', 'Rejected'
                          ) then 'News Digest ML Generated' else nl_name end as nl_name, 
                          email, 
                          sum(
                            case when event_name = 'mail_sent' then 1 else 0 end
                          ) as mail_sent, 
                          sum(
                            case when event_name = 'mail_open' then 1 else 0 end
                          ) as mail_open, 
                          sum(
                            case when event_name = 'mail_click' then 1 else 0 end
                          ) as mail_click 
                        FROM 
                          `et-poc-042021.all_user.EtClientEvents` 
                        WHERE 
                          __time >= cast(current_date - 7 as timestamp) 
                          and client_source = 'et_newsletter' 
                          and event_name in (
                            'mail_sent', 'mail_open', 'mail_click'
                          ) 
                          and nl_name not like '%Test%' 
                          and (
                            mailer_type in ('NEWSLETTER') 
                            or nl_name in ('News Digest')
                          ) 
                          and nl_name not in (
                            'ET Prime Promotions 2', 'ET Tech Promotional', 
                            'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
                            'ETtech Promotional Mailer', 'DeCrypt', 
                            'ETtech News Alert', 'On Board to ET CSV Mailer', 
                            'ET SmallBiz FMC', "ETPrime Today's Edition", 
                            'ET Promotions', 'ET Rise SME', 
                            'ET SmallBiz LUB', 'Unsubscription Mail', 
                            'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
                            'Coronavirus Newsletter', 'ET Specials', 
                            'ET Specials (ET Engaged Users)', 
                            'ET Specials : (ALL ET Audience)', 
                            'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
                          ) 
                        GROUP BY 
                          1, 
                          2, 
                          3
                      ) 
                    group by 
                      1
                  )
              ) 
            where 
              mail_sent != 0 
            union all 
            select 
              a.mon, 
              cast(
                round(
                  cast(mail_sent as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as mail_sent, 
              cast(
                round(
                  cast(mail_open as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as mail_open, 
              cast(
                round(
                  cast(mail_click as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as mail_click, 
              cast(
                round(
                  cast(users as float64)/ cast(day as float64), 
                  0
                ) as float64
              ) as users, 
              case when mail_sent = 0 then 0 else round(
                cast(mail_open as float64)/ cast(mail_sent as float64)* 100, 
                2
              ) end as open_percent, 
              case when mail_sent = 0 then 0 else round(
                cast(mail_click as float64)/ cast(mail_open as float64)* 100, 
                2
              ) end as click_open_percent 
            from 
              (
                select 
                  case when mon = extract(
                    month 
                    from 
                      DATE_SUB(
                        Date(
                          extract(
                            year 
                            from 
                              current_date()
                          ), 
                          extract(
                            month 
                            from 
                              current_date()
                          ), 
                          01
                        ), 
                        INTERVAL 1 MONTH
                      )
                  ) then 'M1' when mon = extract(
                    month 
                    from 
                      DATE_SUB(
                        Date(
                          extract(
                            year 
                            from 
                              current_date()
                          ), 
                          extract(
                            month 
                            from 
                              current_date()
                          ), 
                          01
                        ), 
                        INTERVAL 2 MONTH
                      )
                  ) then 'M2' when mon = extract(
                    month 
                    from 
                      DATE_SUB(
                        Date(
                          extract(
                            year 
                            from 
                              current_date()
                          ), 
                          extract(
                            month 
                            from 
                              current_date()
                          ), 
                          01
                        ), 
                        INTERVAL 3 MONTH
                      )
                  ) then 'M3' when mon = extract(
                    month 
                    from 
                      current_date()
                  ) then 'MTD' end as mon, 
                  sum(mail_sent) as mail_sent, 
                  sum(mail_open) as mail_open, 
                  sum(mail_click) as mail_click, 
                  sum(users) as users, 
                  count(*) as day 
                from 
                  (
                    select 
                      mon, 
                      c_date, 
                      sum(mail_sent) as mail_sent, 
                      sum(mail_open) as mail_open, 
                      sum(mail_click) as mail_click, 
                      sum(
                        case when mail_open != 0 
                        or mail_click != 0 then 1 else 0 end
                      ) as users 
                    from 
                      (
                        SELECT 
                          extract(
                            month 
                            from 
                              cast(
                                SUBSTR(
                                  CAST(
                                    datetime(__time, 'Asia/Kolkata') AS string
                                  ), 
                                  1, 
                                  10
                                ) as Date
                              )
                          ) as mon, 
                          cast(
                            SUBSTR(
                              CAST(
                                datetime(__time, 'Asia/Kolkata') AS string
                              ), 
                              1, 
                              10
                            ) as Date
                          ) as c_date, 
                          case when nl_name = 'News Digest' 
                          and tags in (
                            'rejected_new_mailer', 'Rejected'
                          ) then 'News Digest Static' when nl_name = 'News Digest' 
                          and tags not in (
                            'rejected_new_mailer', 'Rejected'
                          ) then 'News Digest ML Generated' else nl_name end as nl_name, 
                          email, 
                          sum(
                            case when event_name = 'mail_sent' then 1 else 0 end
                          ) as mail_sent, 
                          sum(
                            case when event_name = 'mail_open' then 1 else 0 end
                          ) as mail_open, 
                          sum(
                            case when event_name = 'mail_click' then 1 else 0 end
                          ) as mail_click 
                        FROM 
                          `et-poc-042021.all_user.EtClientEvents` 
                        WHERE 
                          __time >= cast (
                            DATE_SUB(
                              Date(
                                extract(
                                  year 
                                  from 
                                    current_date()
                                ), 
                                extract(
                                  month 
                                  from 
                                    current_date()
                                ), 
                                01
                              ), 
                              INTERVAL 3 MONTH
                            ) as timestamp
                          ) 
                          and client_source = 'et_newsletter' 
                          and event_name in (
                            'mail_sent', 'mail_open', 'mail_click'
                          ) 
                          and nl_name not like '%Test%' 
                          and (
                            mailer_type in ('NEWSLETTER') 
                            or nl_name in ('News Digest')
                          ) 
                          and nl_name not in (
                            'ET Prime Promotions 2', 'ET Tech Promotional', 
                            'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
                            'ETtech Promotional Mailer', 'DeCrypt', 
                            'ETtech News Alert', 'On Board to ET CSV Mailer', 
                            'ET SmallBiz FMC', "ETPrime Today's Edition", 
                            'ET Promotions', 'ET Rise SME', 
                            'ET SmallBiz LUB', 'Unsubscription Mail', 
                            'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
                            'Coronavirus Newsletter', 'ET Specials', 
                            'ET Specials (ET Engaged Users)', 
                            'ET Specials : (ALL ET Audience)', 
                            'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
                          ) 
                        GROUP BY 
                          1, 
                          2, 
                          3, 
                          4
                      ) 
                    group by 
                      1, 
                      2
                  ) 
                group by 
                  1
              ) a
          ) unpivot(
            val for properties in (
              mail_sent, mail_open, mail_click, 
              users, open_percent, click_open_percent
            )
          ) as upv
      ) pivot (
        sum(val) for mon in (
          'D2', 'D3', 'D4', 'last_7_days', 'MTD', 
          'M1', 'M2', 'M3'
        )
      )
  ) 
order by 
  prop_id

~~~

## Newsletter Performance daywise:

### About: It helps to analyze the number of users, newsletter sent,open and clicks for each newsletter day wise for last 15 days

### Query-1:
~~~
select 
  a.c_date, 
  a.nl_name, 
  mail_sent, 
  mail_open, 
  mail_click, 
  users, 
  case when mail_sent = 0 then 0 else round(
    cast(mail_open as float64)/ cast(mail_sent as float64)* 100, 
    2
  ) end as mail_open_percent, 
  case when mail_sent = 0 then 0 else round(
    cast(mail_click as float64)/ cast(mail_sent as float64)* 100, 
    2
  ) end as mail_click_percent 
from 
  (
    SELECT 
      cast(
        SUBSTR(
          CAST(
            datetime(__time, 'Asia/Kolkata') AS string
          ), 
          1, 
          10
        ) as Date
      ) as c_date, 
      nl_name, 
      sum(
        case when event_name = 'mail_sent' then 1 else 0 end
      ) as mail_sent, 
      sum(
        case when event_name = 'mail_open' then 1 else 0 end
      ) as mail_open, 
      sum(
        case when event_name = 'mail_click' then 1 else 0 end
      ) as mail_click 
    FROM 
      `et-poc-042021.all_user.EtClientEvents` 
    WHERE 
      __time >= cast(
        DATETIME_SUB(
          DATETIME_SUB(
            cast(
              CURRENT_DATE()-15 as datetime
            ), 
            INTERVAL 5 HOUR
          ), 
          INTERVAL 30 Minute
        ) as timestamp
      ) 
      and client_source = 'et_newsletter' 
      and event_name in (
        'mail_sent', 'mail_open', 'mail_click'
      ) 
      and nl_name not like '%Test%' 
      and (
        mailer_type in ('NEWSLETTER') 
        or nl_name in ('News Digest')
      ) 
      and nl_name not in (
        'ET Prime Promotions 2', 'ET Tech Promotional', 
        'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
        'ETtech Promotional Mailer', 'DeCrypt', 
        'ETtech News Alert', 'On Board to ET CSV Mailer', 
        'ET SmallBiz FMC', "ETPrime Today's Edition", 
        'ET Promotions', 'ET Rise SME', 
        'ET SmallBiz LUB', 'Unsubscription Mail', 
        'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
        'Coronavirus Newsletter', 'ET Specials', 
        'ET Specials (ET Engaged Users)', 
        'ET Specials : (ALL ET Audience)', 
        'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
      ) 
    GROUP BY 
      1, 
      2
  ) a 
  left join (
    select 
      c_date, 
      nl_name, 
      count(*) as users 
    from 
      (
        SELECT 
          cast(
            SUBSTR(
              CAST(
                datetime(__time, 'Asia/Kolkata') AS string
              ), 
              1, 
              10
            ) as Date
          ) as c_date, 
          nl_name, 
          email 
        FROM 
          `et-poc-042021.all_user.EtClientEvents` 
        WHERE 
          __time >= cast(
            DATETIME_SUB(
              DATETIME_SUB(
                cast(
                  CURRENT_DATE()-15 as datetime
                ), 
                INTERVAL 5 HOUR
              ), 
              INTERVAL 30 Minute
            ) as timestamp
          ) 
          and client_source = 'et_newsletter' 
          and event_name in ('mail_open', 'mail_click') 
          and nl_name not like '%Test%' 
          and (
            mailer_type in ('NEWSLETTER') 
            or nl_name in ('News Digest')
          ) 
          and nl_name not in (
            'ET Prime Promotions 2', 'ET Tech Promotional', 
            'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
            'ETtech Promotional Mailer', 'DeCrypt', 
            'ETtech News Alert', 'On Board to ET CSV Mailer', 
            'ET SmallBiz FMC', "ETPrime Today's Edition", 
            'ET Promotions', 'ET Rise SME', 
            'ET SmallBiz LUB', 'Unsubscription Mail', 
            'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
            'Coronavirus Newsletter', 'ET Specials', 
            'ET Specials (ET Engaged Users)', 
            'ET Specials : (ALL ET Audience)', 
            'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
          ) 
        GROUP BY 
          1, 
          2, 
          3
      ) 
    group by 
      1, 
      2
  ) b on a.c_date = b.c_date 
  and a.nl_name = b.nl_name

~~~
 
 
### Query-2:
~~~
select 
  a.c_date, 
  a.nl_name, 
  mail_sent, 
  mail_open, 
  mail_click, 
  users, 
  case when mail_sent = 0 then 0 else round(
    cast(mail_open as float64)/ cast(mail_sent as float64)* 100, 
    2
  ) end as mail_open_percent, 
  case when mail_sent = 0 then 0 else round(
    cast(mail_click as float64)/ cast(mail_sent as float64)* 100, 
    2
  ) end as mail_click_percent 
from 
  (
    SELECT 
      cast(
        SUBSTR(
          CAST(
            datetime(__time, 'Asia/Kolkata') AS string
          ), 
          1, 
          10
        ) as Date
      ) as c_date, 
      nl_name, 
      sum(
        case when event_name = 'mail_sent' then 1 else 0 end
      ) as mail_sent, 
      sum(
        case when event_name = 'mail_open' then 1 else 0 end
      ) as mail_open, 
      sum(
        case when event_name = 'mail_click' then 1 else 0 end
      ) as mail_click 
    FROM 
      `et-poc-042021.all_user.EtClientEvents` 
    WHERE 
      __time >= cast(
        DATETIME_SUB(
          DATETIME_SUB(
            cast(
              CURRENT_DATE()-15 as datetime
            ), 
            INTERVAL 5 HOUR
          ), 
          INTERVAL 30 Minute
        ) as timestamp
      ) 
      and client_source = 'et_newsletter' 
      and event_name in (
        'mail_sent', 'mail_open', 'mail_click'
      ) 
      and nl_name not like '%Test%' 
      and (
        mailer_type in ('NEWSLETTER') 
        or nl_name in ('News Digest')
      ) 
      and nl_name not in (
        'ET Prime Promotions 2', 'ET Tech Promotional', 
        'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
        'ETtech Promotional Mailer', 'DeCrypt', 
        'ETtech News Alert', 'On Board to ET CSV Mailer', 
        'ET SmallBiz FMC', "ETPrime Today's Edition", 
        'ET Promotions', 'ET Rise SME', 
        'ET SmallBiz LUB', 'Unsubscription Mail', 
        'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
        'Coronavirus Newsletter', 'ET Specials', 
        'ET Specials (ET Engaged Users)', 
        'ET Specials : (ALL ET Audience)', 
        'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
      ) 
    GROUP BY 
      1, 
      2
  ) a 
  left join (
    select 
      c_date, 
      nl_name, 
      count(*) as users 
    from 
      (
        SELECT 
          cast(
            SUBSTR(
              CAST(
                datetime(__time, 'Asia/Kolkata') AS string
              ), 
              1, 
              10
            ) as Date
          ) as c_date, 
          nl_name, 
          email 
        FROM 
          `et-poc-042021.all_user.EtClientEvents` 
        WHERE 
          __time >= cast(
            DATETIME_SUB(
              DATETIME_SUB(
                cast(
                  CURRENT_DATE()-15 as datetime
                ), 
                INTERVAL 5 HOUR
              ), 
              INTERVAL 30 Minute
            ) as timestamp
          ) 
          and client_source = 'et_newsletter' 
          and event_name in ('mail_open', 'mail_click') 
          and nl_name not like '%Test%' 
          and (
            mailer_type in ('NEWSLETTER') 
            or nl_name in ('News Digest')
          ) 
          and nl_name not in (
            'ET Prime Promotions 2', 'ET Tech Promotional', 
            'Promotional CSV Mailer: newsletter@notifications-economictimes.com', 
            'ETtech Promotional Mailer', 'DeCrypt', 
            'ETtech News Alert', 'On Board to ET CSV Mailer', 
            'ET SmallBiz FMC', "ETPrime Today's Edition", 
            'ET Promotions', 'ET Rise SME', 
            'ET SmallBiz LUB', 'Unsubscription Mail', 
            'ET SmallBiz MSMEDF', 'ET SmallBiz GCCI', 
            'Coronavirus Newsletter', 'ET Specials', 
            'ET Specials (ET Engaged Users)', 
            'ET Specials : (ALL ET Audience)', 
            'ET Prime Promotions', 'ET Specials (ET Engaged Users) '
          ) 
        GROUP BY 
          1, 
          2, 
          3
      ) 
    group by 
      1, 
      2
  ) b on a.c_date = b.c_date 
  and a.nl_name = b.nl_name

~~~

## Group Subscription Dashboard:

### About: It helps us to analyze the behavior and engagement of users of group subscriptions on different metrics:
### Sample Queries:
### Query-1:
~~~
Select 
  Group_Name, 
  site_section, 
  MTD, 
  M1, 
  M2, 
  M3 
from 
  (
    select 
      group_name, 
      site_section, 
      MTD, 
      M1, 
      M2, 
      M3, 
      rank() over (
        partition by group_name 
        order by 
          M1 desc
      ) as rnk 
    from 
      (
        select 
          * 
        from 
          (
            select 
              case when month = extract(
                month 
                from 
                  DATE_SUB(
                    Date(
                      extract(
                        year 
                        from 
                          current_date()
                      ), 
                      extract(
                        month 
                        from 
                          current_date()
                      ), 
                      01
                    ), 
                    INTERVAL 1 MONTH
                  )
              ) then 'M1' when month = extract(
                month 
                from 
                  DATE_SUB(
                    Date(
                      extract(
                        year 
                        from 
                          current_date()
                      ), 
                      extract(
                        month 
                        from 
                          current_date()
                      ), 
                      01
                    ), 
                    INTERVAL 2 MONTH
                  )
              ) then 'M2' when month = extract(
                month 
                from 
                  DATE_SUB(
                    Date(
                      extract(
                        year 
                        from 
                          current_date()
                      ), 
                      extract(
                        month 
                        from 
                          current_date()
                      ), 
                      01
                    ), 
                    INTERVAL 3 MONTH
                  )
              ) then 'M3' when month = extract(
                month 
                from 
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  )
              ) then 'MTD' end as Month, 
              group_name, 
              site_section, 
              round(
                cast (users as float64)/ cast(mon_sum as float64)* 100, 
                1
              ) as Monthly_Traffic_Percentage 
            from 
              (
                select 
                  Month, 
                  group_name, 
                  site_section, 
                  users, 
                  sum(users) over (partition by month, group_name) as mon_sum 
                from 
                  (
                    select 
                      group_name, 
                      Month, 
                      site_section, 
                      count(*) as users 
                    from 
                      (
                        select 
                          group_name, 
                          user_email, 
                          Month, 
                          site_section 
                        from 
                          (
                            select 
                              group_name, 
                              user_email 
                            from 
                              (
                                select 
                                  group_name, 
                                  user_subscription_account_id 
                                from 
                                  (
                                    select 
                                      SUBSTR(
                                        SUBSTR(_id, 10), 
                                        1, 
                                        LENGTH(
                                          SUBSTR(_id, 10)
                                        )-1
                                      ) AS _id, 
                                      group_name 
                                    from 
                                      (
                                        select 
                                          SUBSTR(
                                            SUBSTR(string_field_0, 10), 
                                            1, 
                                            LENGTH(
                                              SUBSTR(string_field_0, 10)
                                            )-1
                                          ) AS id, 
                                          string_field_3 as group_name 
                                        from 
                                          all_user.groups 
                                        where 
                                          string_field_4 = 'ACTIVE'
                                      ) a 
                                      inner join et - poc - 042021.all_user.grp_subscrip c on group_id = id
                                  ) a 
                                  inner join et - poc - 042021.all_user.grp_subscrip_map b on group_subscription_id = a._id 
                                group by 
                                  1, 
                                  2
                              ) a 
                              inner join (
                                SELECT 
                                  SUBSTR(
                                    SUBSTR(_id, 10), 
                                    1, 
                                    LENGTH(
                                      SUBSTR(_id, 10)
                                    )-1
                                  ) AS id, 
                                  user_email 
                                FROM 
                                  `et-poc-042021.all_user.user_subscription_accounts` 
                                GROUP BY 
                                  id, 
                                  user_email
                              ) b on user_subscription_account_id = id 
                            group by 
                              1, 
                              2
                          ) a 
                          inner join (
                            select 
                              email, 
                              extract(
                                month 
                                from 
                                  c_date
                              ) as Month, 
                              site_section 
                            from 
                              (
                                select 
                                  gid, 
                                  cast(
                                    SUBSTR(
                                      CAST(
                                        datetime(__time, 'Asia/Kolkata') AS string
                                      ), 
                                      1, 
                                      10
                                    ) as Date
                                  ) as c_date, 
                                  site_section 
                                from 
                                  `et-poc-042021.all_user.EtClientEvents` 
                                where 
                                  __time >= cast(
                                    DATETIME_SUB(
                                      DATETIME_SUB(
                                        cast (
                                          DATE_SUB(
                                            Date(
                                              extract(
                                                year 
                                                from 
                                                  current_date()
                                              ), 
                                              extract(
                                                month 
                                                from 
                                                  current_date()
                                              ), 
                                              01
                                            ), 
                                            INTERVAL 3 MONTH
                                          ) as timestamp
                                        ), 
                                        INTERVAL 5 Hour
                                      ), 
                                      INTERVAL 30 minute
                                    ) as timestamp
                                  ) 
                                  and client_source = 'growth_rx' 
                                  and event_name = 'page_view' 
                                  and (
                                    url like '%articleshow%' 
                                    or page_template like '%articleshow%'
                                  ) 
                                  and et_product not in (
                                    'Behind Sign In', 'Advertorial', 
                                    'homepage'
                                  ) 
                                group by 
                                  1, 
                                  2, 
                                  3
                              ) a 
                              inner join (
                                select 
                                  gid, 
                                  email 
                                from 
                                  `et-poc-042021.all_user.EtClientEvents` 
                                where 
                                  __time >= cast(
                                    DATETIME_SUB(
                                      DATETIME_SUB(
                                        cast (
                                          DATE_SUB(
                                            Date(
                                              extract(
                                                year 
                                                from 
                                                  current_date()
                                              ), 
                                              extract(
                                                month 
                                                from 
                                                  current_date()
                                              ), 
                                              01
                                            ), 
                                            INTERVAL 3 MONTH
                                          ) as timestamp
                                        ), 
                                        INTERVAL 5 Hour
                                      ), 
                                      INTERVAL 30 minute
                                    ) as timestamp
                                  ) 
                                  and gid is not null 
                                  and email is not null 
                                  and gid not in (
                                    '00000000-0000-0000-0000-000000000000'
                                  ) 
                                group by 
                                  1, 
                                  2
                              ) e on e.gid = a.gid 
                            group by 
                              1, 
                              2, 
                              3
                          ) b on a.user_email = b.email 
                        group by 
                          1, 
                          2, 
                          3, 
                          4
                      ) 
                    where 
                      site_section not in ('HOME') 
                    group by 
                      1, 
                      2, 
                      3
                  )
              )
          ) pivot(
            sum(Monthly_Traffic_Percentage) for Month in ('MTD', 'M1', 'M2', 'M3')
          )
      )
  ) 
where 
  rnk <= 5 
order by 
  Group_Name

~~~
### Query-2:
~~~
select 
  * 
from 
  (
    select 
      case when month = extract(
        month 
        from 
          DATE_SUB(
            Date(
              extract(
                year 
                from 
                  current_date()
              ), 
              extract(
                month 
                from 
                  current_date()
              ), 
              01
            ), 
            INTERVAL 1 MONTH
          )
      ) then 'M1' when month = extract(
        month 
        from 
          DATE_SUB(
            Date(
              extract(
                year 
                from 
                  current_date()
              ), 
              extract(
                month 
                from 
                  current_date()
              ), 
              01
            ), 
            INTERVAL 2 MONTH
          )
      ) then 'M2' when month = extract(
        month 
        from 
          DATE_SUB(
            Date(
              extract(
                year 
                from 
                  current_date()
              ), 
              extract(
                month 
                from 
                  current_date()
              ), 
              01
            ), 
            INTERVAL 3 MONTH
          )
      ) then 'M3' when month = extract(
        month 
        from 
          Date(
            extract(
              year 
              from 
                current_date()
            ), 
            extract(
              month 
              from 
                current_date()
            ), 
            01
          )
      ) then 'MTD' end as Month, 
      site_section, 
      round(
        cast (users as float64)/ cast(mon_sum as float64)* 100, 
        1
      ) as Monthly_Traffic_Percentage 
    from 
      (
        select 
          Month, 
          site_section, 
          users, 
          sum(users) over (partition by month) as mon_sum 
        from 
          (
            select 
              Month, 
              site_section, 
              count(*) as users 
            from 
              (
                select 
                  user_email, 
                  Month, 
                  site_section 
                from 
                  (
                    select 
                      group_name, 
                      user_email 
                    from 
                      (
                        select 
                          group_name, 
                          user_subscription_account_id 
                        from 
                          (
                            select 
                              SUBSTR(
                                SUBSTR(_id, 10), 
                                1, 
                                LENGTH(
                                  SUBSTR(_id, 10)
                                )-1
                              ) AS _id, 
                              group_name 
                            from 
                              (
                                select 
                                  SUBSTR(
                                    SUBSTR(string_field_0, 10), 
                                    1, 
                                    LENGTH(
                                      SUBSTR(string_field_0, 10)
                                    )-1
                                  ) AS id, 
                                  string_field_3 as group_name 
                                from 
                                  all_user.groups 
                                where 
                                  string_field_4 = 'ACTIVE'
                              ) a 
                              inner join et - poc - 042021.all_user.grp_subscrip c on group_id = id
                          ) a 
                          inner join et - poc - 042021.all_user.grp_subscrip_map b on group_subscription_id = a._id 
                        group by 
                          1, 
                          2
                      ) a 
                      inner join (
                        SELECT 
                          SUBSTR(
                            SUBSTR(_id, 10), 
                            1, 
                            LENGTH(
                              SUBSTR(_id, 10)
                            )-1
                          ) AS id, 
                          user_email 
                        FROM 
                          `et-poc-042021.all_user.user_subscription_accounts` 
                        GROUP BY 
                          id, 
                          user_email
                      ) b on user_subscription_account_id = id 
                    group by 
                      1, 
                      2
                  ) a 
                  inner join (
                    select 
                      email, 
                      extract(
                        month 
                        from 
                          c_date
                      ) as Month, 
                      site_section 
                    from 
                      (
                        select 
                          gid, 
                          cast(
                            SUBSTR(
                              CAST(
                                datetime(__time, 'Asia/Kolkata') AS string
                              ), 
                              1, 
                              10
                            ) as Date
                          ) as c_date, 
                          site_section 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast (
                                  DATE_SUB(
                                    Date(
                                      extract(
                                        year 
                                        from 
                                          current_date()
                                      ), 
                                      extract(
                                        month 
                                        from 
                                          current_date()
                                      ), 
                                      01
                                    ), 
                                    INTERVAL 3 MONTH
                                  ) as timestamp
                                ), 
                                INTERVAL 5 Hour
                              ), 
                              INTERVAL 30 minute
                            ) as timestamp
                          ) 
                          and client_source = 'growth_rx' 
                          and event_name = 'page_view' 
                          and (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and et_product not in (
                            'Behind Sign In', 'Advertorial', 
                            'homepage'
                          ) 
                        group by 
                          1, 
                          2, 
                          3
                      ) a 
                      inner join (
                        select 
                          gid, 
                          email 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast (
                                  DATE_SUB(
                                    Date(
                                      extract(
                                        year 
                                        from 
                                          current_date()
                                      ), 
                                      extract(
                                        month 
                                        from 
                                          current_date()
                                      ), 
                                      01
                                    ), 
                                    INTERVAL 3 MONTH
                                  ) as timestamp
                                ), 
                                INTERVAL 5 Hour
                              ), 
                              INTERVAL 30 minute
                            ) as timestamp
                          ) 
                          and gid is not null 
                          and email is not null 
                          and gid not in (
                            '00000000-0000-0000-0000-000000000000'
                          ) 
                        group by 
                          1, 
                          2
                      ) e on e.gid = a.gid 
                    group by 
                      1, 
                      2, 
                      3
                  ) b on a.user_email = b.email 
                group by 
                  1, 
                  2, 
                  3
              ) 
            where 
              site_section not in ('HOME') 
            group by 
              1, 
              2
          )
      )
  ) pivot(
    sum(Monthly_Traffic_Percentage) for Month in ('MTD', 'M1', 'M2', 'M3')
  ) 
order by 
  M1 desc 
limit 
  5

~~~

### Query-3:
~~~
select 
  * 
from 
  (
    select 
      month, 
      group_name as Group_Name, 
      cast(val as float64) as val, 
      x as Metrics 
    from 
      (
        select 
          case when month = extract(
            month 
            from 
              DATE_SUB(
                Date(
                  extract(
                    year 
                    from 
                      current_date()
                  ), 
                  extract(
                    month 
                    from 
                      current_date()
                  ), 
                  01
                ), 
                INTERVAL 1 MONTH
              )
          ) then 'M1' when month = extract(
            month 
            from 
              DATE_SUB(
                Date(
                  extract(
                    year 
                    from 
                      current_date()
                  ), 
                  extract(
                    month 
                    from 
                      current_date()
                  ), 
                  01
                ), 
                INTERVAL 2 MONTH
              )
          ) then 'M2' when month = extract(
            month 
            from 
              DATE_SUB(
                Date(
                  extract(
                    year 
                    from 
                      current_date()
                  ), 
                  extract(
                    month 
                    from 
                      current_date()
                  ), 
                  01
                ), 
                INTERVAL 3 MONTH
              )
          ) then 'M3' when month = extract(
            month 
            from 
              Date(
                extract(
                  year 
                  from 
                    current_date()
                ), 
                extract(
                  month 
                  from 
                    current_date()
                ), 
                01
              )
          ) then 'MTD' end as Month, 
          group_name, 
          cast(Total_Users as string) as Total_Users, 
          cast(MAU as string) as MAU, 
          cast(DAU as string) as DAU, 
          cast(Total_active_days as string) as Total_active_days, 
          cast(
            Total_active_days_Per_User as string
          ) as Total_active_days_Per_User, 
          cast(Total_Sessions as string) as Total_Sessions, 
          cast(Avg_Session_Per_User as string) as Avg_Session_Per_User, 
          cast(Total_Articles_Read as string) as Total_Articles_Read, 
          cast(
            Total_Articles_Read_per_user as string
          ) as Total_Articles_Read_per_user, 
          cast(
            Articles_Read_Per_User_Per_Day as string
          ) as Articles_Read_Per_User_Per_Day, 
          cast(Prime_Articles_Read as string) as Prime_Articles_Read, 
          cast(Premium_Articles_Read as string) as Premium_Articles_Read 
        from 
          (
            select 
              a.Month, 
              a.group_name, 
              cast(Total_Users as float64) as Total_Users, 
              MAU, 
              cast(dau_avg as float64) as DAU, 
              sessions as Total_Sessions, 
              round(
                cast(sessions as float64)/ cast(MAU as float64), 
                0
              ) as Avg_Session_Per_User, 
              Total_Articles_Read, 
              round(
                cast(Total_Articles_Read as float64)/ cast(MAU as float64), 
                1
              ) as Total_Articles_Read_per_user, 
              round(
                cast(Total_Articles_Read as float64)/ cast(active_days as float64), 
                1
              ) as Articles_Read_Per_User_Per_Day, 
              active_days as Total_active_days, 
              round(
                cast(active_days as float64)/ cast(MAU as float64), 
                1
              ) as Total_active_days_Per_User, 
              prime as Prime_Articles_Read, 
              premium as Premium_Articles_Read 
            from 
              (
                select 
                  Month, 
                  group_name, 
                  count(*) as MAU, 
                  sum(Active_days) as active_days, 
                  sum(Free) as free, 
                  sum(premium) as premium, 
                  sum(Prime) as prime, 
                  sum(Total_articles_read) as Total_Articles_Read, 
                  sum(sessions) as sessions 
                from 
                  (
                    select 
                      Month, 
                      group_name, 
                      user_email, 
                      coalesce(active_days, 0) as Active_days, 
                      coalesce(Free, 0) Free, 
                      coalesce(premium, 0) as premium, 
                      coalesce(Prime, 0) as prime, 
                      coalesce(Total_articles_read, 0) as Total_articles_read, 
                      coalesce(sessions, 0) as sessions 
                    from 
                      (
                        select 
                          group_name, 
                          user_email 
                        from 
                          (
                            select 
                              group_name, 
                              user_subscription_account_id 
                            from 
                              (
                                select 
                                  SUBSTR(
                                    SUBSTR(_id, 10), 
                                    1, 
                                    LENGTH(
                                      SUBSTR(_id, 10)
                                    )-1
                                  ) AS _id, 
                                  group_name 
                                from 
                                  (
                                    select 
                                      SUBSTR(
                                        SUBSTR(string_field_0, 10), 
                                        1, 
                                        LENGTH(
                                          SUBSTR(string_field_0, 10)
                                        )-1
                                      ) AS id, 
                                      string_field_3 as group_name 
                                    from 
                                      all_user.groups 
                                    where 
                                      string_field_4 = 'ACTIVE'
                                  ) a 
                                  inner join et - poc - 042021.all_user.grp_subscrip c on group_id = id
                              ) a 
                              inner join et - poc - 042021.all_user.grp_subscrip_map b on group_subscription_id = a._id 
                            group by 
                              1, 
                              2
                          ) a 
                          inner join (
                            SELECT 
                              SUBSTR(
                                SUBSTR(_id, 10), 
                                1, 
                                LENGTH(
                                  SUBSTR(_id, 10)
                                )-1
                              ) AS id, 
                              user_email 
                            FROM 
                              `et-poc-042021.all_user.user_subscription_accounts` 
                            GROUP BY 
                              id, 
                              user_email
                          ) b on user_subscription_account_id = id 
                        group by 
                          1, 
                          2
                      ) a 
                      inner join (
                        Select 
                          ad.Month, 
                          ad.email, 
                          active_days, 
                          Free, 
                          premium, 
                          Prime, 
                          coalesce(Free, 0)+ coalesce(premium, 0)+ coalesce(prime, 0) as Total_articles_read, 
                          sessions 
                        from 
                          (
                            select 
                              extract(
                                month 
                                from 
                                  c_date
                              ) as Month, 
                              email, 
                              count(*) as active_days 
                            from 
                              (
                                select 
                                  email, 
                                  c_date 
                                from 
                                  (
                                    select 
                                      gid, 
                                      cast(
                                        SUBSTR(
                                          CAST(
                                            datetime(__time, 'Asia/Kolkata') AS string
                                          ), 
                                          1, 
                                          10
                                        ) as Date
                                      ) as c_date 
                                    from 
                                      `et-poc-042021.all_user.EtClientEvents` 
                                    where 
                                      __time >= cast(
                                        DATETIME_SUB(
                                          DATETIME_SUB(
                                            cast (
                                              DATE_SUB(
                                                Date(
                                                  extract(
                                                    year 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  extract(
                                                    month 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  01
                                                ), 
                                                INTERVAL 3 MONTH
                                              ) as timestamp
                                            ), 
                                            INTERVAL 5 Hour
                                          ), 
                                          INTERVAL 30 minute
                                        ) as timestamp
                                      ) 
                                      and client_source = 'growth_rx' 
                                      and event_name not in (
                                        'Profile', 'expire', 'push_subscription', 
                                        'renew', 'uninstalled'
                                      ) 
                                    group by 
                                      1, 
                                      2
                                  ) a 
                                  inner join (
                                    select 
                                      gid, 
                                      email 
                                    from 
                                      `et-poc-042021.all_user.EtClientEvents` 
                                    where 
                                      __time >= cast(
                                        DATETIME_SUB(
                                          DATETIME_SUB(
                                            cast (
                                              DATE_SUB(
                                                Date(
                                                  extract(
                                                    year 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  extract(
                                                    month 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  01
                                                ), 
                                                INTERVAL 3 MONTH
                                              ) as timestamp
                                            ), 
                                            INTERVAL 5 Hour
                                          ), 
                                          INTERVAL 30 minute
                                        ) as timestamp
                                      ) 
                                      and gid is not null 
                                      and email is not null 
                                      and gid not in (
                                        '00000000-0000-0000-0000-000000000000'
                                      ) 
                                    group by 
                                      1, 
                                      2
                                  ) e on e.gid = a.gid 
                                group by 
                                  1, 
                                  2
                              ) 
                            group by 
                              1, 
                              2
                          ) ad 
                          left join (
                            select 
                              Month, 
                              email, 
                              sum(premium) as premium, 
                              sum(prime) as prime, 
                              sum(Free) as Free 
                            from 
                              (
                                select 
                                  Month, 
                                  email, 
                                  sum(
                                    case when et_product = 'Premium' then article_read else 0 end
                                  ) as premium, 
                                  sum(
                                    case when et_product = 'Prime Plus' then article_read else 0 end
                                  ) as prime, 
                                  sum(
                                    case when (
                                      (
                                        et_product not in ('Premium', 'Prime Plus')
                                      ) 
                                      or (et_product is null)
                                    ) then article_read else 0 end
                                  ) as Free 
                                from 
                                  (
                                    select 
                                      Month, 
                                      email, 
                                      et_product, 
                                      count(*) as article_read 
                                    from 
                                      (
                                        select 
                                          Month, 
                                          email, 
                                          et_product, 
                                          msid 
                                        from 
                                          (
                                            select 
                                              extract(
                                                month 
                                                from 
                                                  cast(
                                                    SUBSTR(
                                                      CAST(
                                                        datetime(__time, 'Asia/Kolkata') AS string
                                                      ), 
                                                      1, 
                                                      10
                                                    ) as Date
                                                  )
                                              ) as Month, 
                                              gid, 
                                              et_product, 
                                              msid 
                                            from 
                                              `et-poc-042021.all_user.EtClientEvents` 
                                            where 
                                              __time >= cast(
                                                DATETIME_SUB(
                                                  DATETIME_SUB(
                                                    cast (
                                                      DATE_SUB(
                                                        Date(
                                                          extract(
                                                            year 
                                                            from 
                                                              current_date()
                                                          ), 
                                                          extract(
                                                            month 
                                                            from 
                                                              current_date()
                                                          ), 
                                                          01
                                                        ), 
                                                        INTERVAL 3 MONTH
                                                      ) as timestamp
                                                    ), 
                                                    INTERVAL 5 Hour
                                                  ), 
                                                  INTERVAL 30 minute
                                                ) as timestamp
                                              ) 
                                              and event_name = 'page_view' 
                                              and (
                                                url like '%articleshow%' 
                                                or page_template like '%articleshow%'
                                              ) 
                                              and et_product not in (
                                                'Behind Sign In', 'Advertorial', 
                                                'homepage'
                                              ) 
                                            group by 
                                              1, 
                                              2, 
                                              3, 
                                              4
                                          ) x 
                                          inner join (
                                            select 
                                              gid, 
                                              email 
                                            from 
                                              `et-poc-042021.all_user.EtClientEvents` 
                                            where 
                                              __time >= cast(
                                                DATETIME_SUB(
                                                  DATETIME_SUB(
                                                    cast (
                                                      DATE_SUB(
                                                        Date(
                                                          extract(
                                                            year 
                                                            from 
                                                              current_date()
                                                          ), 
                                                          extract(
                                                            month 
                                                            from 
                                                              current_date()
                                                          ), 
                                                          01
                                                        ), 
                                                        INTERVAL 3 MONTH
                                                      ) as timestamp
                                                    ), 
                                                    INTERVAL 5 Hour
                                                  ), 
                                                  INTERVAL 30 minute
                                                ) as timestamp
                                              ) 
                                              and gid is not null 
                                              and email is not null 
                                              and gid not in (
                                                '00000000-0000-0000-0000-000000000000'
                                              ) 
                                            group by 
                                              1, 
                                              2
                                          ) e on x.gid = e.gid 
                                        group by 
                                          1, 
                                          2, 
                                          3, 
                                          4
                                      ) 
                                    group by 
                                      1, 
                                      2, 
                                      3
                                  ) 
                                group by 
                                  1, 
                                  2
                              ) 
                            group by 
                              1, 
                              2
                          ) ar on ad.email = ar.email 
                          and ad.Month = ar.Month 
                          left join (
                            select 
                              Month, 
                              email, 
                              count(*) as sessions 
                            from 
                              (
                                select 
                                  Month, 
                                  email, 
                                  sessionId 
                                from 
                                  (
                                    select 
                                      extract(
                                        month 
                                        from 
                                          cast(
                                            SUBSTR(
                                              CAST(
                                                datetime(__time, 'Asia/Kolkata') AS string
                                              ), 
                                              1, 
                                              10
                                            ) as Date
                                          )
                                      ) as Month, 
                                      gid, 
                                      sessionId 
                                    from 
                                      `et-poc-042021.all_user.EtClientEvents` 
                                    where 
                                      __time >= cast(
                                        DATETIME_SUB(
                                          DATETIME_SUB(
                                            cast (
                                              DATE_SUB(
                                                Date(
                                                  extract(
                                                    year 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  extract(
                                                    month 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  01
                                                ), 
                                                INTERVAL 3 MONTH
                                              ) as timestamp
                                            ), 
                                            INTERVAL 5 Hour
                                          ), 
                                          INTERVAL 30 minute
                                        ) as timestamp
                                      ) 
                                      and client_source = 'growth_rx' 
                                      and event_name not in (
                                        'Profile', 'expire', 'push_subscription', 
                                        'renew', 'uninstalled'
                                      ) 
                                    group by 
                                      1, 
                                      2, 
                                      3
                                  ) a 
                                  inner join (
                                    select 
                                      gid, 
                                      email 
                                    from 
                                      `et-poc-042021.all_user.EtClientEvents` 
                                    where 
                                      __time >= cast(
                                        DATETIME_SUB(
                                          DATETIME_SUB(
                                            cast (
                                              DATE_SUB(
                                                Date(
                                                  extract(
                                                    year 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  extract(
                                                    month 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  01
                                                ), 
                                                INTERVAL 3 MONTH
                                              ) as timestamp
                                            ), 
                                            INTERVAL 5 Hour
                                          ), 
                                          INTERVAL 30 minute
                                        ) as timestamp
                                      ) 
                                      and gid is not null 
                                      and email is not null 
                                      and gid not in (
                                        '00000000-0000-0000-0000-000000000000'
                                      ) 
                                    group by 
                                      1, 
                                      2
                                  ) e on e.gid = a.gid 
                                where 
                                  sessionId is not null 
                                group by 
                                  1, 
                                  2, 
                                  3
                              ) 
                            group by 
                              1, 
                              2
                          ) s on ad.email = s.email 
                          and ad.Month = s.Month
                      ) b on a.user_email = b.email 
                    order by 
                      Month desc
                  ) 
                group by 
                  1, 
                  2
              ) a 
              left join (
                select 
                  month, 
                  group_name, 
                  round(
                    cast(dau as float64)/ cast(days as float64), 
                    0
                  ) as dau_avg, 
                  dau as Total_Users 
                from 
                  (
                    select 
                      month, 
                      group_name, 
                      count(*) as days, 
                      sum(dau) as dau 
                    from 
                      (
                        select 
                          month, 
                          group_name, 
                          c_date, 
                          count(*) as dau 
                        from 
                          (
                            select 
                              extract(
                                month 
                                from 
                                  c_date
                              ) as month, 
                              group_name, 
                              email, 
                              c_date 
                            from 
                              (
                                select 
                                  email, 
                                  c_date 
                                from 
                                  (
                                    select 
                                      gid, 
                                      cast(
                                        SUBSTR(
                                          CAST(
                                            datetime(__time, 'Asia/Kolkata') AS string
                                          ), 
                                          1, 
                                          10
                                        ) as Date
                                      ) as c_date 
                                    from 
                                      `et-poc-042021.all_user.EtClientEvents` 
                                    where 
                                      __time >= cast(
                                        DATETIME_SUB(
                                          DATETIME_SUB(
                                            cast (
                                              DATE_SUB(
                                                Date(
                                                  extract(
                                                    year 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  extract(
                                                    month 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  01
                                                ), 
                                                INTERVAL 3 MONTH
                                              ) as timestamp
                                            ), 
                                            INTERVAL 5 Hour
                                          ), 
                                          INTERVAL 30 minute
                                        ) as timestamp
                                      ) 
                                      and client_source = 'growth_rx' 
                                      and event_name not in (
                                        'Profile', 'expire', 'push_subscription', 
                                        'renew', 'uninstalled'
                                      ) 
                                    group by 
                                      1, 
                                      2
                                  ) a 
                                  inner join (
                                    select 
                                      gid, 
                                      email 
                                    from 
                                      `et-poc-042021.all_user.EtClientEvents` 
                                    where 
                                      __time >= cast(
                                        DATETIME_SUB(
                                          DATETIME_SUB(
                                            cast (
                                              DATE_SUB(
                                                Date(
                                                  extract(
                                                    year 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  extract(
                                                    month 
                                                    from 
                                                      current_date()
                                                  ), 
                                                  01
                                                ), 
                                                INTERVAL 3 MONTH
                                              ) as timestamp
                                            ), 
                                            INTERVAL 5 Hour
                                          ), 
                                          INTERVAL 30 minute
                                        ) as timestamp
                                      ) 
                                      and gid is not null 
                                      and email is not null 
                                      and gid not in (
                                        '00000000-0000-0000-0000-000000000000'
                                      ) 
                                    group by 
                                      1, 
                                      2
                                  ) e on e.gid = a.gid 
                                group by 
                                  1, 
                                  2
                              ) a 
                              inner join (
                                select 
                                  group_name, 
                                  user_email 
                                from 
                                  (
                                    select 
                                      group_name, 
                                      user_subscription_account_id 
                                    from 
                                      (
                                        select 
                                          SUBSTR(
                                            SUBSTR(_id, 10), 
                                            1, 
                                            LENGTH(
                                              SUBSTR(_id, 10)
                                            )-1
                                          ) AS _id, 
                                          group_name 
                                        from 
                                          (
                                            select 
                                              SUBSTR(
                                                SUBSTR(string_field_0, 10), 
                                                1, 
                                                LENGTH(
                                                  SUBSTR(string_field_0, 10)
                                                )-1
                                              ) AS id, 
                                              string_field_3 as group_name 
                                            from 
                                              all_user.groups 
                                            where 
                                              string_field_4 = 'ACTIVE'
                                          ) a 
                                          inner join et - poc - 042021.all_user.grp_subscrip c on group_id = id
                                      ) a 
                                      inner join et - poc - 042021.all_user.grp_subscrip_map b on group_subscription_id = a._id 
                                    group by 
                                      1, 
                                      2
                                  ) a 
                                  inner join (
                                    SELECT 
                                      SUBSTR(
                                        SUBSTR(_id, 10), 
                                        1, 
                                        LENGTH(
                                          SUBSTR(_id, 10)
                                        )-1
                                      ) AS id, 
                                      user_email 
                                    FROM 
                                      `et-poc-042021.all_user.user_subscription_accounts` 
                                    GROUP BY 
                                      id, 
                                      user_email
                                  ) b on user_subscription_account_id = id 
                                group by 
                                  1, 
                                  2
                              ) b on a.email = b.user_email 
                            group by 
                              1, 
                              2, 
                              3, 
                              4
                          ) 
                        group by 
                          1, 
                          2, 
                          3
                      ) 
                    group by 
                      1, 
                      2
                  )
              ) b on a.group_name = b.group_name 
              and a.month = b.month
          )
      ) unpivot(
        val for x in (
          Total_Users, MAU, DAU, Total_active_days, 
          Total_active_days_Per_User, Total_Sessions, 
          Avg_Session_Per_User, Total_Articles_Read, 
          Total_Articles_Read_per_user, Articles_Read_Per_User_Per_Day, 
          Prime_Articles_Read, Premium_Articles_Read
        )
      )
  ) pivot (
    sum(val) for month in ('MTD', 'M1', 'M2', 'M3')
  )

~~~
 
## RFV Analysis for Paid Users:

### About: This report is used to analyze the behavior of paid users based on their RFV month on month
### Query-1:
~~~
select 
  email, 
  extract(
    year 
    from 
      subscription_start_date
  ) as subscr_year, 
  extract(
    Month 
    from 
      subscription_start_date
  ) as subscr_month, 
  v_buck, 
  f_buck, 
  r_buck, 
  duration, 
  rfv_conc_score, 
  rfv_sum_score, 
  post_v_buck, 
  post_f_buck, 
  post_r_buck, 
  subscription_status 
from 
  `et-poc-042021.paid.users_score`

~~~
### Query-2:
 
~~~
select 
  a.email, 
  v_buck, 
  f_buck, 
  r_buck, 
  rfv_conc_score, 
  rfv_sum_score, 
  a.duration, 
  b.duration as subscription_month, 
  b.subscription_status 
from 
  (
    select 
      v_buck, 
      f_buck, 
      r_buck, 
      rfv_conc_score, 
      rfv_sum_score, 
      duration, 
      email, 
      subscription_status, 
      case when duration = 'January' then 1 when duration = 'February' then 2 when duration = 'March' then 3 when duration = 'April' then 4 end as month 
    from 
      `et-poc-042021.paid.users_score`
  ) a 
  inner join (
    select 
      * 
    from 
      (
        select 
          email, 
          case when duration = 'January' then 1 when duration = 'February' then 2 when duration = 'March' then 3 when duration = 'April' then 4 end as month, 
          duration, 
          subscription_status, 
          subscription_end_date 
        from 
          `et-poc-042021.paid.users_score` 
        where 
          subscription_status in ('CANCELLED', 'EXPIRED')
      ) 
    where 
      month = extract(
        month 
        from 
          subscription_end_date
      )
  ) b on a.email = b.email 
  and b.month > a.month 
order by 
  a.email

~~~
### Query-3:
~~~
select 
  a.email, 
  CASE WHEN a.month = b.month THEN pre_v_buck else v_buck END AS v_buck, 
  CASE WHEN a.month = b.month THEN pre_rfv_conc_score else rfv_conc_score END AS rfv_conc_score, 
  CASE WHEN a.month = b.month THEN pre_rfv_sum_score else rfv_sum_score END AS rfv_sum_score, 
  a.duration, 
  b.duration as cancelled_month 
from 
  (
    select 
      duration, 
      email, 
      trial_status, 
      v_buck, 
      case when duration = 'January' then 1 when duration = 'February' then 2 when duration = 'March' then 3 when duration = 'April' then 4 end as month, 
      rfv_conc_score, 
      rfv_sum_score 
    from 
      `et-poc-042021.paid.users_score`
  ) a 
  inner join (
    select 
      email, 
      case when duration = 'January' then 1 when duration = 'February' then 2 when duration = 'March' then 3 when duration = 'April' then 4 end as month, 
      duration, 
      pre_v_buck, 
      pre_rfv_conc_score, 
      pre_rfv_sum_score 
    from 
      `et-poc-042021.paid.users_score` 
    where 
      subscription_status = 'CANCELLED' 
      and (
        pre_freq != 0 
        or pre_recency != 0
      )
  ) b on a.email = b.email 
  and b.month >= a.month 
where 
  a.trial_status = 'contnued_after_trial' 
order by 
  a.email

~~~
## Table usage: User_score to show frequency and recency pattern for users
## Users Direct & Organic Engagement On Articles

## About: This report is used to find the user base and page views by the users on a day level

### Query:
~~~
select 
  c_date, 
  count(*) as Users, 
  sum(upv) as Unique_PV, 
  sum(pv) as PV 
from 
  (
    select 
      c_date, 
      gid, 
      count(*) as upv, 
      sum(pv) as pv 
    from 
      (
        SELECT 
          cast(
            SUBSTR(
              CAST(__time AS string), 
              1, 
              10
            ) as Date
          ) as c_date, 
          gid, 
          url, 
          count(*) as pv 
        FROM 
          `et-poc-042021.all_user.EtClientEvents` 
        WHERE 
          __time >= cast(CURRENT_DATE - 10 as timestamp) 
          and __time < cast(CURRENT_DATE as timestamp) 
          and client_source = 'growth_rx' 
          and event_name = 'page_view' 
          and (
            source = 'organic' 
            or platform in ('AMP')
          ) 
          and url like '%articleshow%' 
        GROUP BY 
          1, 
          2, 
          3
      ) 
    group by 
      1, 
      2
  ) 
group by 
  1 
order by 
  c_date desc

~~~
## Paid User Engagement:
## About: This report helps to analyze the behavior of users based on different parameters like active days, volume,time of activity, type of articles, based on session, based on newsletter engagement ,user’s bucket , platform etc on monthly,weekly and daily comparison

### Query:
~~~
select 
  C1, 
  yesterday, 
  WOW_Avg, 
  MTD, 
  M1, 
  M2, 
  M3 
from 
  (
    Select 
      a.C1, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      'A1' as ord, 
    from 
      (
        select 
          'Active_users' as C1, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M3 
        from 
          (
            select 
              month, 
              cast(active_users as float64)/ cast(days as float64) as Avg_active_users 
            from 
              (
                select 
                  extract(
                    month 
                    from 
                      c_date
                  ) as month, 
                  count(*) as days, 
                  sum(user) as active_users 
                from 
                  (
                    select 
                      c_date, 
                      count(*) as user 
                    from 
                      (
                        select 
                          email, 
                          c_date 
                        from 
                          (
                            select 
                              gid, 
                              cast(
                                SUBSTR(
                                  CAST(
                                    datetime(__time, 'Asia/Kolkata') AS string
                                  ), 
                                  1, 
                                  10
                                ) as Date
                              ) as c_date 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and (
                                lower(user_subscription_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%active%' 
                                or lower(user_subscription_status) like '%active%'
                              ) 
                              and event_name = 'page_view' 
                              and (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              ) 
                            group by 
                              1, 
                              2
                          ) a 
                          inner join (
                            select 
                              gid, 
                              email 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and gid is not null 
                              and email is not null 
                              and gid not in (
                                '00000000-0000-0000-0000-000000000000'
                              ) 
                            group by 
                              1, 
                              2
                          ) e on e.gid = a.gid 
                        group by 
                          1, 
                          2
                      ) 
                    group by 
                      1
                  ) 
                group by 
                  1
              )
          )
      ) a 
      left join (
        select 
          'Active_users' as C1, 
          count(*) as yesterday 
        from 
          (
            select 
              email 
            from 
              (
                select 
                  gid 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-1 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and __time < cast(
                    current_date() as timestamp
                  ) 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                  and event_name = 'page_view' 
                  and (
                    url like '%articleshow%' 
                    or page_template like '%articleshow%'
                  ) 
                group by 
                  1
              ) a 
              inner join (
                select 
                  gid, 
                  email 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-30 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and gid is not null 
                  and email is not null 
                  and gid not in (
                    '00000000-0000-0000-0000-000000000000'
                  ) 
                group by 
                  1, 
                  2
              ) e on e.gid = a.gid 
            group by 
              1
          ) 
        group by 
          1
      ) b on a.C1 = b.C1 
      left join (
        select 
          'Active_users' as C1, 
          cast(
            sum(Active_users)/ 4 as int64
          ) as WOW_Avg 
        from 
          (
            select 
              c_date, 
              count(*) as Active_users 
            from 
              (
                select 
                  email, 
                  c_date 
                from 
                  (
                    select 
                      gid, 
                      cast(
                        SUBSTR(
                          CAST(
                            datetime(__time, 'Asia/Kolkata') AS string
                          ), 
                          1, 
                          10
                        ) as Date
                      ) as c_date 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-29 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      ) 
                      and EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          date (
                            datetime(__time, 'Asia/Kolkata')
                          )
                      )= EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          current_date - 1
                      ) 
                    group by 
                      1, 
                      2
                  ) a 
                  inner join (
                    select 
                      gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        current_date()-56 as timestamp
                      ) 
                      and gid is not null 
                      and email is not null 
                      and gid not in (
                        '00000000-0000-0000-0000-000000000000'
                      ) 
                    group by 
                      1, 
                      2
                  ) e on e.gid = a.gid 
                group by 
                  1, 
                  2
              ) 
            group by 
              1
          )
      ) c on a.C1 = c.C1 
    UNION ALL 
    select 
      a.C1, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      'B1' as ord 
    from 
      (
        select 
          'Sessions' as C1, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then cast(
              Round(
                cast(session as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then cast(
              Round(
                cast(session as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then cast(
              Round(
                cast(session as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then cast(
              Round(
                cast(session as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M3 
        from 
          (
            select 
              extract(
                month 
                from 
                  c_date
              ) as month, 
              sum(session) as session, 
              count(*) as days 
            from 
              (
                select 
                  cast(
                    SUBSTR(
                      CAST(
                        datetime(__time, 'Asia/Kolkata') AS string
                      ), 
                      1, 
                      10
                    ) as Date
                  ) as c_date, 
                  sum(is_new_session) as session 
                from 
                  (
                    SELECT 
                      *, 
                      CASE WHEN UNIX_SECONDS(__time) - UNIX_SECONDS(last_event) >= (60 * 120) 
                      OR last_event IS NULL THEN 1 ELSE 0 END AS is_new_session 
                    FROM 
                      (
                        SELECT 
                          email, 
                          __time, 
                          LAG(__time, 1) OVER (
                            PARTITION BY email 
                            ORDER BY 
                              __time
                          ) AS last_event 
                        from 
                          (
                            select 
                              gid, 
                              __time 
                            FROM 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and (
                                lower(user_subscription_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%active%' 
                                or lower(user_subscription_status) like '%active%'
                              ) 
                              and event_name = 'page_view' 
                              and (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              )
                          ) a 
                          inner join (
                            select 
                              gid, 
                              email 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and gid is not null 
                              and email is not null 
                              and gid not in (
                                '00000000-0000-0000-0000-000000000000'
                              ) 
                            group by 
                              1, 
                              2
                          ) e on e.gid = a.gid
                      ) last
                  ) 
                group by 
                  1
              ) 
            group by 
              1
          )
      ) a 
      left join (
        select 
          'Sessions' as C1, 
          sum(session) as yesterday 
        from 
          (
            select 
              email, 
              sum(is_new_session) as session 
            from 
              (
                SELECT 
                  *, 
                  CASE WHEN UNIX_SECONDS(__time) - UNIX_SECONDS(last_event) >= (60 * 120) 
                  OR last_event IS NULL THEN 1 ELSE 0 END AS is_new_session 
                FROM 
                  (
                    SELECT 
                      email, 
                      __time, 
                      LAG(__time, 1) OVER (
                        PARTITION BY email 
                        ORDER BY 
                          __time
                      ) AS last_event 
                    from 
                      (
                        select 
                          gid, 
                          __time 
                        FROM 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast(
                                  CURRENT_DATE()-1 as datetime
                                ), 
                                INTERVAL 5 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and __time < cast(
                            current_date() as timestamp
                          ) 
                          and (
                            lower(user_subscription_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%active%' 
                            or lower(user_subscription_status) like '%active%'
                          ) 
                          and event_name = 'page_view' 
                          and (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          )
                      ) a 
                      inner join (
                        select 
                          gid, 
                          email 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast(
                                  CURRENT_DATE()-30 as datetime
                                ), 
                                INTERVAL 5 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and gid is not null 
                          and email is not null 
                          and gid not in (
                            '00000000-0000-0000-0000-000000000000'
                          ) 
                        group by 
                          1, 
                          2
                      ) e on e.gid = a.gid
                  ) last
              ) 
            group by 
              1
          ) 
        group by 
          1
      ) b on a.C1 = b.C1 
      left join (
        select 
          'Sessions' as C1, 
          cast(
            sum(session)/ 4 as int64
          ) as WOW_Avg 
        from 
          (
            select 
              email, 
              sum(is_new_session) as session 
            from 
              (
                SELECT 
                  *, 
                  CASE WHEN UNIX_SECONDS(__time) - UNIX_SECONDS(last_event) >= (60 * 120) 
                  OR last_event IS NULL THEN 1 ELSE 0 END AS is_new_session 
                FROM 
                  (
                    SELECT 
                      email, 
                      __time, 
                      LAG(__time, 1) OVER (
                        PARTITION BY email 
                        ORDER BY 
                          __time
                      ) AS last_event 
                    from 
                      (
                        select 
                          gid, 
                          __time 
                        FROM 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast(
                                  CURRENT_DATE()-29 as datetime
                                ), 
                                INTERVAL 5 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and __time < cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast(
                                  CURRENT_DATE()-1 as datetime
                                ), 
                                INTERVAL 5 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and (
                            lower(user_subscription_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%active%' 
                            or lower(user_subscription_status) like '%active%'
                          ) 
                          and event_name = 'page_view' 
                          and (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and EXTRACT(
                            DAYOFWEEK 
                            FROM 
                              date (
                                datetime(__time, 'Asia/Kolkata')
                              )
                          )= EXTRACT(
                            DAYOFWEEK 
                            FROM 
                              current_date - 1
                          )
                      ) a 
                      inner join (
                        select 
                          gid, 
                          email 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            current_date()-56 as timestamp
                          ) 
                          and gid is not null 
                          and email is not null 
                          and gid not in (
                            '00000000-0000-0000-0000-000000000000'
                          ) 
                        group by 
                          1, 
                          2
                      ) e on e.gid = a.gid
                  ) last
              ) 
            group by 
              1
          ) 
        group by 
          1
      ) c on a.C1 = c.C1 
    union all 
    select 
      a.session_category, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      concat(
        'C', 
        cast(ord as string)
      ) as ord 
    from 
      (
        select 
          session_category, 
          case when session_category = 'Early morning' then 1 when session_category = 'late morning' then 2 when session_category = 'Afternoon' then 3 when session_category = 'Early Evening' then 4 when session_category = 'late Evening' then 5 when session_category = 'Late night' then 6 end as ord, 
          'Session_category' as C1, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then cast(
              Round(
                cast(session as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then cast(
              Round(
                cast(session as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then cast(
              Round(
                cast(session as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then cast(
              Round(
                cast(session as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M3 
        from 
          (
            select 
              session_category, 
              extract(
                month 
                from 
                  c_date
              ) as month, 
              sum(session) as session, 
              count(*) as days 
            from 
              (
                select 
                  case when cast(
                    SUBSTR(
                      CAST(
                        cast (
                          datetime(__time, 'Asia/Kolkata') as timestamp
                        ) AS string
                      ), 
                      12, 
                      2
                    ) as int64
                  ) between 0 
                  and 6 
                  or cast(
                    SUBSTR(
                      CAST(
                        cast (
                          datetime(__time, 'Asia/Kolkata') as timestamp
                        ) AS string
                      ), 
                      12, 
                      2
                    ) as int64
                  ) between 22 
                  and 23 then 'Late night' when cast(
                    SUBSTR(
                      CAST(
                        cast (
                          datetime(__time, 'Asia/Kolkata') as timestamp
                        ) AS string
                      ), 
                      12, 
                      2
                    ) as int64
                  ) between 7 
                  and 9 then 'Early morning' when cast(
                    SUBSTR(
                      CAST(
                        cast (
                          datetime(__time, 'Asia/Kolkata') as timestamp
                        ) AS string
                      ), 
                      12, 
                      2
                    ) as int64
                  ) between 10 
                  and 11 then 'late morning' when cast(
                    SUBSTR(
                      CAST(
                        cast (
                          datetime(__time, 'Asia/Kolkata') as timestamp
                        ) AS string
                      ), 
                      12, 
                      2
                    ) as int64
                  ) between 12 
                  and 14 then 'Afternoon' when cast(
                    SUBSTR(
                      CAST(
                        cast (
                          datetime(__time, 'Asia/Kolkata') as timestamp
                        ) AS string
                      ), 
                      12, 
                      2
                    ) as int64
                  ) between 15 
                  and 17 then 'Early Evening' when cast(
                    SUBSTR(
                      CAST(
                        cast (
                          datetime(__time, 'Asia/Kolkata') as timestamp
                        ) AS string
                      ), 
                      12, 
                      2
                    ) as int64
                  ) between 18 
                  and 21 then 'late Evening' end as session_category, 
                  cast(
                    SUBSTR(
                      CAST(
                        datetime(__time, 'Asia/Kolkata') AS string
                      ), 
                      1, 
                      10
                    ) as Date
                  ) as c_date, 
                  sum(is_new_session) as session 
                from 
                  (
                    SELECT 
                      *, 
                      CASE WHEN UNIX_SECONDS(__time) - UNIX_SECONDS(last_event) >= (60 * 120) 
                      OR last_event IS NULL THEN 1 ELSE 0 END AS is_new_session 
                    FROM 
                      (
                        SELECT 
                          email, 
                          __time, 
                          LAG(__time, 1) OVER (
                            PARTITION BY email 
                            ORDER BY 
                              __time
                          ) AS last_event 
                        from 
                          (
                            select 
                              gid, 
                              __time 
                            FROM 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and (
                                lower(user_subscription_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%active%' 
                                or lower(user_subscription_status) like '%active%'
                              ) 
                              and event_name = 'page_view' 
                              and (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              )
                          ) a 
                          inner join (
                            select 
                              gid, 
                              email 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and gid is not null 
                              and email is not null 
                              and gid not in (
                                '00000000-0000-0000-0000-000000000000'
                              ) 
                            group by 
                              1, 
                              2
                          ) e on e.gid = a.gid
                      ) last
                  ) 
                group by 
                  1, 
                  2
              ) 
            group by 
              1, 
              2
          ) 
        group by 
          1, 
          2
      ) a 
      left join (
        select 
          'Session_category' as C1, 
          case when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 0 
          and 6 
          or cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 22 
          and 23 then 'Late night' when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 7 
          and 9 then 'Early morning' when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 10 
          and 11 then 'late morning' when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 12 
          and 14 then 'Afternoon' when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 15 
          and 17 then 'Early Evening' when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 18 
          and 21 then 'late Evening' end as session_category, 
          sum(is_new_session) as yesterday 
        from 
          (
            SELECT 
              *, 
              CASE WHEN UNIX_SECONDS(__time) - UNIX_SECONDS(last_event) >= (60 * 120) 
              OR last_event IS NULL THEN 1 ELSE 0 END AS is_new_session 
            FROM 
              (
                SELECT 
                  email, 
                  __time, 
                  LAG(__time, 1) OVER (
                    PARTITION BY email 
                    ORDER BY 
                      __time
                  ) AS last_event 
                from 
                  (
                    select 
                      gid, 
                      __time 
                    FROM 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        current_date() as timestamp
                      ) 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      )
                  ) a 
                  inner join (
                    select 
                      gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-30 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and gid is not null 
                      and email is not null 
                      and gid not in (
                        '00000000-0000-0000-0000-000000000000'
                      ) 
                    group by 
                      1, 
                      2
                  ) e on e.gid = a.gid
              ) last
          ) 
        group by 
          1, 
          2
      ) b on a.C1 = b.C1 
      and a.session_category = b.session_category 
      left join (
        select 
          'Session_category' as C1, 
          case when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 0 
          and 6 
          or cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 22 
          and 23 then 'Late night' when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 7 
          and 9 then 'Early morning' when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 10 
          and 11 then 'late morning' when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 12 
          and 14 then 'Afternoon' when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 15 
          and 17 then 'Early Evening' when cast(
            SUBSTR(
              CAST(
                cast (
                  datetime(__time, 'Asia/Kolkata') as timestamp
                ) AS string
              ), 
              12, 
              2
            ) as int64
          ) between 18 
          and 21 then 'late Evening' end as session_category, 
          cast(
            sum(is_new_session)/ 4 as int64
          ) as WOW_Avg 
        from 
          (
            SELECT 
              *, 
              CASE WHEN UNIX_SECONDS(__time) - UNIX_SECONDS(last_event) >= (60 * 120) 
              OR last_event IS NULL THEN 1 ELSE 0 END AS is_new_session 
            FROM 
              (
                SELECT 
                  email, 
                  __time, 
                  LAG(__time, 1) OVER (
                    PARTITION BY email 
                    ORDER BY 
                      __time
                  ) AS last_event 
                from 
                  (
                    select 
                      gid, 
                      __time 
                    FROM 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-29 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      ) 
                      and EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          date (
                            datetime(__time, 'Asia/Kolkata')
                          )
                      )= EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          current_date - 1
                      )
                  ) a 
                  inner join (
                    select 
                      gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        current_date()-56 as timestamp
                      ) 
                      and gid is not null 
                      and email is not null 
                      and gid not in (
                        '00000000-0000-0000-0000-000000000000'
                      ) 
                    group by 
                      1, 
                      2
                  ) e on e.gid = a.gid
              ) last
          ) 
        group by 
          1, 
          2
      ) c on c.C1 = a.C1 
      and a.session_category = c.Session_category --order by ord asc
    Union all 
    select 
      a.C1, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      'D' as ord 
    from 
      (
        select 
          'Total_articles_read' as C1, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then cast(
              Round(
                cast(Total_articles_read as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then cast(
              Round(
                cast(Total_articles_read as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then cast(
              Round(
                cast(Total_articles_read as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then cast(
              Round(
                cast(Total_articles_read as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M3 
        from 
          (
            select 
              extract(
                month 
                from 
                  c_date
              ) as month, 
              sum(Total_articles_read) as Total_articles_read, 
              count(*) as days 
            from 
              (
                select 
                  c_date, 
                  sum(volume) as Total_articles_read 
                from 
                  (
                    select 
                      c_date, 
                      email, 
                      count(*) as volume 
                    from 
                      (
                        select 
                          email, 
                          msid, 
                          c_date 
                        from 
                          (
                            select 
                              gid, 
                              cast(
                                SUBSTR(
                                  CAST(
                                    datetime(__time, 'Asia/Kolkata') AS string
                                  ), 
                                  1, 
                                  10
                                ) as Date
                              ) as c_date, 
                              msid 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and event_name = 'page_view' 
                              and (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              ) 
                              and (
                                lower(user_subscription_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%active%' 
                                or lower(user_subscription_status) like '%active%'
                              ) 
                              and et_product not in (
                                'Behind Sign In', 'Advertorial', 
                                'homepage'
                              ) 
                            group by 
                              1, 
                              2, 
                              3
                          ) a 
                          inner join (
                            select 
                              gid, 
                              email 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and gid is not null 
                              and email is not null 
                              and gid not in (
                                '00000000-0000-0000-0000-000000000000'
                              ) 
                            group by 
                              1, 
                              2
                          ) e on e.gid = a.gid 
                        group by 
                          1, 
                          2, 
                          3
                      ) 
                    group by 
                      1, 
                      2
                  ) 
                group by 
                  1
              ) 
            group by 
              1
          ) 
        group by 
          1
      ) a 
      left join (
        select 
          'Total_articles_read' as C1, 
          sum(volume) as yesterday 
        from 
          (
            select 
              email, 
              count(*) as volume 
            from 
              (
                select 
                  email, 
                  msid 
                from 
                  (
                    select 
                      gid, 
                      et_product, 
                      msid 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        current_date() as timestamp
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      ) 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                      and et_product not in (
                        'Behind Sign In', 'Advertorial', 
                        'homepage'
                      ) 
                    group by 
                      1, 
                      2, 
                      3
                  ) a 
                  inner join (
                    select 
                      gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-30 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and gid is not null 
                      and email is not null 
                      and gid not in (
                        '00000000-0000-0000-0000-000000000000'
                      ) 
                    group by 
                      1, 
                      2
                  ) e on e.gid = a.gid 
                group by 
                  1, 
                  2
              ) 
            group by 
              1
          )
      ) b on a.C1 = b.C1 
      left join (
        select 
          'Total_articles_read' as C1, 
          cast (
            sum(Total_articles_read)/ 4 as int64
          ) as WOW_Avg 
        from 
          (
            select 
              c_date, 
              sum(volume) as Total_articles_read 
            from 
              (
                select 
                  email, 
                  c_date, 
                  count(*) as volume 
                from 
                  (
                    select 
                      email, 
                      msid, 
                      c_date 
                    from 
                      (
                        select 
                          gid, 
                          cast(
                            SUBSTR(
                              CAST(
                                datetime(__time, 'Asia/Kolkata') AS string
                              ), 
                              1, 
                              10
                            ) as Date
                          ) as c_date, 
                          msid 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast(
                                  CURRENT_DATE()-29 as datetime
                                ), 
                                INTERVAL 5 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and __time < cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast(
                                  CURRENT_DATE()-1 as datetime
                                ), 
                                INTERVAL 5 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and event_name = 'page_view' 
                          and (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and (
                            lower(user_subscription_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%active%' 
                            or lower(user_subscription_status) like '%active%'
                          ) 
                          and et_product not in (
                            'Behind Sign In', 'Advertorial', 
                            'homepage'
                          ) 
                          and EXTRACT(
                            DAYOFWEEK 
                            FROM 
                              date (
                                datetime(__time, 'Asia/Kolkata')
                              )
                          )= EXTRACT(
                            DAYOFWEEK 
                            FROM 
                              current_date - 1
                          ) 
                        group by 
                          1, 
                          2, 
                          3
                      ) a 
                      inner join (
                        select 
                          gid, 
                          email 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            current_date()-60 as timestamp
                          ) 
                          and gid is not null 
                          and email is not null 
                          and gid not in (
                            '00000000-0000-0000-0000-000000000000'
                          ) 
                        group by 
                          1, 
                          2
                      ) e on e.gid = a.gid 
                    group by 
                      1, 
                      2, 
                      3
                  ) 
                group by 
                  1, 
                  2
              ) 
            group by 
              1
          ) 
        group by 
          1
      ) c on a.C1 = c.C1 
    union all 
    select 
      a.volume, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      concat(
        'E', 
        cast(ord as string)
      ) as ord 
    from 
      (
        select 
          volume, 
          case when volume = '1' then 1 when volume = '2' then 2 when volume = '3' then 3 when volume = '4 to 5' then 4 when volume = '6 to 7' then 5 when volume = '8 to 10' then 6 when volume = '> 11' then 7 end as ord, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then cast(
              Round(
                cast(user as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then cast(
              Round(
                cast(user as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then cast(
              Round(
                cast(user as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then cast(
              Round(
                cast(user as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M3 
        from 
          (
            select 
              extract(
                month 
                from 
                  c_date
              ) as month, 
              volume, 
              sum(user) as user, 
              count(*) as days 
            from 
              (
                select 
                  c_date, 
                  case when volume = 1 then '1' when volume = 2 then '2' when volume = 3 then '3' when volume >= 4 
                  and volume < 6 then '4 to 5' when volume >= 6 
                  and volume < 8 then '6 to 7' when volume >= 8 
                  and volume < 11 then '8 to 10' when volume >= 11 then '> 11' end as volume, 
                  sum(user) as user 
                from 
                  (
                    select 
                      c_date, 
                      volume, 
                      count(*) as user 
                    from 
                      (
                        select 
                          c_date, 
                          email, 
                          count(*) as volume 
                        from 
                          (
                            select 
                              email, 
                              msid, 
                              c_date 
                            from 
                              (
                                select 
                                  gid, 
                                  cast(
                                    SUBSTR(
                                      CAST(
                                        datetime(__time, 'Asia/Kolkata') AS string
                                      ), 
                                      1, 
                                      10
                                    ) as Date
                                  ) as c_date, 
                                  msid 
                                from 
                                  `et-poc-042021.all_user.EtClientEvents` 
                                where 
                                  __time >= cast(
                                    DATETIME_SUB(
                                      DATETIME_SUB(
                                        cast (
                                          DATE_SUB(
                                            Date(
                                              extract(
                                                year 
                                                from 
                                                  current_date()
                                              ), 
                                              extract(
                                                month 
                                                from 
                                                  current_date()
                                              ), 
                                              01
                                            ), 
                                            INTERVAL 3 MONTH
                                          ) as timestamp
                                        ), 
                                        INTERVAL 5 Hour
                                      ), 
                                      INTERVAL 30 minute
                                    ) as timestamp
                                  ) 
                                  and __time < cast(
                                    current_date() as timestamp
                                  ) 
                                  and event_name = 'page_view' 
                                  and (
                                    url like '%articleshow%' 
                                    or page_template like '%articleshow%'
                                  ) 
                                  and (
                                    lower(user_subscription_status) like '%paid%' 
                                    or lower(current_subscriber_status) like '%paid%' 
                                    or lower(current_subscriber_status) like '%active%' 
                                    or lower(user_subscription_status) like '%active%'
                                  ) 
                                  and et_product not in (
                                    'Behind Sign In', 'Advertorial', 
                                    'homepage'
                                  ) 
                                group by 
                                  1, 
                                  2, 
                                  3
                              ) a 
                              inner join (
                                select 
                                  gid, 
                                  email 
                                from 
                                  `et-poc-042021.all_user.EtClientEvents` 
                                where 
                                  __time >= cast(
                                    DATETIME_SUB(
                                      DATETIME_SUB(
                                        cast (
                                          DATE_SUB(
                                            Date(
                                              extract(
                                                year 
                                                from 
                                                  current_date()
                                              ), 
                                              extract(
                                                month 
                                                from 
                                                  current_date()
                                              ), 
                                              01
                                            ), 
                                            INTERVAL 3 MONTH
                                          ) as timestamp
                                        ), 
                                        INTERVAL 5 Hour
                                      ), 
                                      INTERVAL 30 minute
                                    ) as timestamp
                                  ) 
                                  and __time < cast(
                                    current_date() as timestamp
                                  ) 
                                  and gid is not null 
                                  and email is not null 
                                  and gid not in (
                                    '00000000-0000-0000-0000-000000000000'
                                  ) 
                                group by 
                                  1, 
                                  2
                              ) e on e.gid = a.gid 
                            group by 
                              1, 
                              2, 
                              3
                          ) 
                        group by 
                          1, 
                          2
                      ) 
                    group by 
                      1, 
                      2
                  ) 
                group by 
                  1, 
                  2
              ) 
            group by 
              1, 
              2
          ) 
        group by 
          1, 
          2
      ) a 
      left join (
        select 
          case when volume = 1 then '1' when volume = 2 then '2' when volume = 3 then '3' when volume >= 4 
          and volume < 6 then '4 to 5' when volume >= 6 
          and volume < 8 then '6 to 7' when volume >= 8 
          and volume < 11 then '8 to 10' when volume >= 11 then '> 11' end as volume, 
          sum(user) as yesterday 
        from 
          (
            select 
              volume, 
              count(*) as user 
            from 
              (
                select 
                  email, 
                  count(*) as volume 
                from 
                  (
                    select 
                      email, 
                      msid 
                    from 
                      (
                        select 
                          gid, 
                          et_product, 
                          msid 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast(
                                  CURRENT_DATE()-1 as datetime
                                ), 
                                INTERVAL 5 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and __time < cast(
                            current_date() as timestamp
                          ) 
                          and event_name = 'page_view' 
                          and (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and (
                            lower(user_subscription_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%active%' 
                            or lower(user_subscription_status) like '%active%'
                          ) 
                          and et_product not in (
                            'Behind Sign In', 'Advertorial', 
                            'homepage'
                          ) 
                        group by 
                          1, 
                          2, 
                          3
                      ) a 
                      inner join (
                        select 
                          gid, 
                          email 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast(
                                  CURRENT_DATE()-30 as datetime
                                ), 
                                INTERVAL 5 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and gid is not null 
                          and email is not null 
                          and gid not in (
                            '00000000-0000-0000-0000-000000000000'
                          ) 
                        group by 
                          1, 
                          2
                      ) e on e.gid = a.gid 
                    group by 
                      1, 
                      2
                  ) 
                group by 
                  1
              ) 
            group by 
              1
          ) 
        group by 
          1
      ) b on a.volume = b.volume 
      left join (
        select 
          case when volume = 1 then '1' when volume = 2 then '2' when volume = 3 then '3' when volume >= 4 
          and volume < 6 then '4 to 5' when volume >= 6 
          and volume < 8 then '6 to 7' when volume >= 8 
          and volume < 11 then '8 to 10' when volume >= 11 then '> 11' end as volume, 
          cast (
            sum(user)/ 4 as int64
          ) as WOW_Avg 
        from 
          (
            select 
              c_date, 
              volume, 
              count(*) as user 
            from 
              (
                select 
                  email, 
                  c_date, 
                  count(*) as volume 
                from 
                  (
                    select 
                      email, 
                      msid, 
                      c_date 
                    from 
                      (
                        select 
                          gid, 
                          cast(
                            SUBSTR(
                              CAST(
                                datetime(__time, 'Asia/Kolkata') AS string
                              ), 
                              1, 
                              10
                            ) as Date
                          ) as c_date, 
                          msid 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast(
                                  CURRENT_DATE()-29 as datetime
                                ), 
                                INTERVAL 5 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and __time < cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast(
                                  CURRENT_DATE()-1 as datetime
                                ), 
                                INTERVAL 5 HOUR
                              ), 
                              INTERVAL 30 Minute
                            ) as timestamp
                          ) 
                          and event_name = 'page_view' 
                          and (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and (
                            lower(user_subscription_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%active%' 
                            or lower(user_subscription_status) like '%active%'
                          ) 
                          and et_product not in (
                            'Behind Sign In', 'Advertorial', 
                            'homepage'
                          ) 
                          and EXTRACT(
                            DAYOFWEEK 
                            FROM 
                              date (
                                datetime(__time, 'Asia/Kolkata')
                              )
                          )= EXTRACT(
                            DAYOFWEEK 
                            FROM 
                              current_date - 1
                          ) 
                        group by 
                          1, 
                          2, 
                          3
                      ) a 
                      inner join (
                        select 
                          gid, 
                          email 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            current_date()-60 as timestamp
                          ) 
                          and gid is not null 
                          and email is not null 
                          and gid not in (
                            '00000000-0000-0000-0000-000000000000'
                          ) 
                        group by 
                          1, 
                          2
                      ) e on e.gid = a.gid 
                    group by 
                      1, 
                      2, 
                      3
                  ) 
                group by 
                  1, 
                  2
              ) 
            group by 
              1, 
              2
          ) 
        group by 
          1
      ) c on a.volume = c.volume --order by ord asc
    union all 
    select 
      a.story_type, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      'F' as ord 
    from 
      (
        select 
          story_type, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then cast(
              Round(
                cast(volume as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then cast(
              Round(
                cast(volume as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then cast(
              Round(
                cast(volume as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then cast(
              Round(
                cast(volume as float64)/ cast(days as float64), 
                0
              ) as int64
            ) else 0 end
          ) as M3 
        from 
          (
            select 
              extract(
                month 
                from 
                  c_date
              ) as month, 
              Story_type, 
              sum(volume) as volume, 
              count(*) as days 
            from 
              (
                select 
                  c_date, 
                  Story_type, 
                  count(*) as volume 
                from 
                  (
                    select 
                      email, 
                      case when et_product = 'Premium' then 'premium' when et_product = 'Prime Plus' then 'prime' else 'Free' end as Story_type, 
                      c_date, 
                      msid 
                    from 
                      (
                        select 
                          gid, 
                          et_product, 
                          msid, 
                          cast(
                            SUBSTR(
                              CAST(
                                datetime(__time, 'Asia/Kolkata') AS string
                              ), 
                              1, 
                              10
                            ) as Date
                          ) as c_date 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast (
                                  DATE_SUB(
                                    Date(
                                      extract(
                                        year 
                                        from 
                                          current_date()
                                      ), 
                                      extract(
                                        month 
                                        from 
                                          current_date()
                                      ), 
                                      01
                                    ), 
                                    INTERVAL 3 MONTH
                                  ) as timestamp
                                ), 
                                INTERVAL 5 Hour
                              ), 
                              INTERVAL 30 minute
                            ) as timestamp
                          ) 
                          and __time < cast(
                            current_date() as timestamp
                          ) 
                          and event_name = 'page_view' 
                          and (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and (
                            lower(user_subscription_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%paid%' 
                            or lower(current_subscriber_status) like '%active%' 
                            or lower(user_subscription_status) like '%active%'
                          ) 
                          and et_product not in (
                            'Behind Sign In', 'Advertorial', 
                            'homepage'
                          ) 
                        group by 
                          1, 
                          2, 
                          3, 
                          4
                      ) a 
                      inner join (
                        select 
                          gid, 
                          email 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast (
                                  DATE_SUB(
                                    Date(
                                      extract(
                                        year 
                                        from 
                                          current_date()
                                      ), 
                                      extract(
                                        month 
                                        from 
                                          current_date()
                                      ), 
                                      01
                                    ), 
                                    INTERVAL 3 MONTH
                                  ) as timestamp
                                ), 
                                INTERVAL 5 Hour
                              ), 
                              INTERVAL 30 minute
                            ) as timestamp
                          ) 
                          and __time < cast(
                            current_date() as timestamp
                          ) 
                          and gid is not null 
                          and email is not null 
                          and gid not in (
                            '00000000-0000-0000-0000-000000000000'
                          ) 
                        group by 
                          1, 
                          2
                      ) e on e.gid = a.gid 
                    group by 
                      1, 
                      2, 
                      3, 
                      4
                  ) 
                group by 
                  1, 
                  2
              ) 
            group by 
              1, 
              2
          ) 
        group by 
          1
      ) a 
      left join (
        select 
          Story_type, 
          count(*) as yesterday 
        from 
          (
            select 
              email, 
              case when et_product = 'Premium' then 'premium' when et_product = 'Prime Plus' then 'prime' else 'Free' end as Story_type, 
              msid 
            from 
              (
                select 
                  gid, 
                  et_product, 
                  msid 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-1 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and __time < cast(
                    current_date() as timestamp
                  ) 
                  and event_name = 'page_view' 
                  and (
                    url like '%articleshow%' 
                    or page_template like '%articleshow%'
                  ) 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                  and et_product not in (
                    'Behind Sign In', 'Advertorial', 
                    'homepage'
                  ) 
                group by 
                  1, 
                  2, 
                  3
              ) a 
              inner join (
                select 
                  gid, 
                  email 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-30 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and gid is not null 
                  and email is not null 
                  and gid not in (
                    '00000000-0000-0000-0000-000000000000'
                  ) 
                group by 
                  1, 
                  2
              ) e on e.gid = a.gid 
            group by 
              1, 
              2, 
              3
          ) 
        group by 
          1
      ) b on a.story_type = b.story_type 
      left join (
        select 
          Story_type, 
          cast(
            count(*)/ 4 as int64
          ) as WOW_Avg 
        from 
          (
            select 
              email, 
              case when et_product = 'Premium' then 'premium' when et_product = 'Prime Plus' then 'prime' else 'Free' end as Story_type, 
              msid, 
              c_date 
            from 
              (
                select 
                  gid, 
                  et_product, 
                  msid, 
                  cast(
                    SUBSTR(
                      CAST(
                        datetime(__time, 'Asia/Kolkata') AS string
                      ), 
                      1, 
                      10
                    ) as Date
                  ) as c_date 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-29 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and __time < cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-1 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and event_name = 'page_view' 
                  and (
                    url like '%articleshow%' 
                    or page_template like '%articleshow%'
                  ) 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                  and et_product not in (
                    'Behind Sign In', 'Advertorial', 
                    'homepage'
                  ) 
                  and EXTRACT(
                    DAYOFWEEK 
                    FROM 
                      date (
                        datetime(__time, 'Asia/Kolkata')
                      )
                  )= EXTRACT(
                    DAYOFWEEK 
                    FROM 
                      current_date - 1
                  ) 
                group by 
                  1, 
                  2, 
                  3, 
                  4
              ) a 
              inner join (
                select 
                  gid, 
                  email 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= cast(
                    current_date()-60 as timestamp
                  ) 
                  and gid is not null 
                  and email is not null 
                  and gid not in (
                    '00000000-0000-0000-0000-000000000000'
                  ) 
                group by 
                  1, 
                  2
              ) e on e.gid = a.gid 
            group by 
              1, 
              2, 
              3, 
              4
          ) 
        group by 
          1
      ) c on a.story_type = c.story_type 
    Union all 
    Select 
      a.C1, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      concat(
        'G', 
        cast(p as string)
      ) as ord, 
    from 
      (
        select 
          platform as C1, 
          case when platform = 'desktop' then 1 when platform = 'android' then 2 when platform = 'ios' then 3 when platform = 'mweb' then 4 end as p, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M3 
        from 
          (
            select 
              platform, 
              month, 
              cast(active_users as float64)/ cast(days as float64) as Avg_active_users 
            from 
              (
                select 
                  platform, 
                  extract(
                    month 
                    from 
                      c_date
                  ) as month, 
                  count(*) as days, 
                  sum(user) as active_users 
                from 
                  (
                    select 
                      platform, 
                      c_date, 
                      count(*) as user 
                    from 
                      (
                        select 
                          platform, 
                          email, 
                          c_date 
                        from 
                          (
                            select 
                              case when (
                                (platform = 'desktop') 
                                or (
                                  platform = 'web' 
                                  and device_category in ('desktop', 'tablet')
                                )
                              ) then 'desktop' when (
                                (
                                  platform = 'web' 
                                  and device_category = 'mobile'
                                ) 
                                or platform in ('amp', 'pwa', 'PWA', 'AMP', 'mweb')
                              ) then 'mweb' else platform end as platform, 
                              gid, 
                              cast(
                                SUBSTR(
                                  CAST(
                                    datetime(__time, 'Asia/Kolkata') AS string
                                  ), 
                                  1, 
                                  10
                                ) as Date
                              ) as c_date 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and (
                                lower(user_subscription_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%active%' 
                                or lower(user_subscription_status) like '%active%'
                              ) 
                              and client_source = 'growth_rx' 
                              and event_name = 'page_view' 
                              and (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              ) 
                            group by 
                              1, 
                              2, 
                              3
                          ) a 
                          inner join (
                            select 
                              g.gid, 
                              email 
                            from 
                              `et-poc-042021.all_user.gid_email` g 
                            group by 
                              1, 
                              2
                          ) e on e.gid = a.gid 
                        group by 
                          1, 
                          2, 
                          3
                      ) 
                    group by 
                      1, 
                      2
                  ) 
                group by 
                  1, 
                  2
              )
          ) 
        group by 
          1
      ) a 
      left join (
        select 
          platform as C1, 
          count(*) as yesterday 
        from 
          (
            select 
              platform, 
              email 
            from 
              (
                select 
                  gid, 
                  case when (
                    (platform = 'desktop') 
                    or (
                      platform = 'web' 
                      and device_category in ('desktop', 'tablet')
                    )
                  ) then 'desktop' when (
                    platform = 'web' 
                    and device_category = 'mobile'
                  ) 
                  or platform in ('amp', 'pwa', 'PWA', 'AMP', 'mweb') then 'mweb' else platform end as platform 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-1 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and __time < cast(
                    current_date() as timestamp
                  ) 
                  and event_name = 'page_view' 
                  and (
                    url like '%articleshow%' 
                    or page_template like '%articleshow%'
                  ) 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                  and client_source = 'growth_rx' 
                group by 
                  1, 
                  2
              ) a 
              inner join (
                select 
                  g.gid, 
                  email 
                from 
                  `et-poc-042021.all_user.gid_email` g 
                group by 
                  1, 
                  2
              ) e on e.gid = a.gid 
            group by 
              1, 
              2
          ) 
        group by 
          1
      ) b on a.C1 = b.C1 
      left join (
        select 
          platform as C1, 
          cast(
            sum(Active_users)/ 4 as int64
          ) as WOW_Avg 
        from 
          (
            select 
              platform, 
              c_date, 
              count(*) as Active_users 
            from 
              (
                select 
                  platform, 
                  email, 
                  c_date 
                from 
                  (
                    select 
                      case when (
                        (platform = 'desktop') 
                        or (
                          platform = 'web' 
                          and device_category in ('desktop', 'tablet')
                        )
                      ) then 'desktop' when (
                        platform = 'web' 
                        and device_category = 'mobile'
                      ) 
                      or platform in ('amp', 'pwa', 'PWA', 'AMP', 'mweb') then 'mweb' else platform end as platform, 
                      gid, 
                      cast(
                        SUBSTR(
                          CAST(
                            datetime(__time, 'Asia/Kolkata') AS string
                          ), 
                          1, 
                          10
                        ) as Date
                      ) as c_date 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-29 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      ) 
                      and client_source = 'growth_rx' 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                      and EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          date (
                            datetime(__time, 'Asia/Kolkata')
                          )
                      )= EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          current_date - 1
                      ) 
                    group by 
                      1, 
                      2, 
                      3
                  ) a 
                  inner join (
                    select 
                      g.gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.gid_email` g 
                    group by 
                      1, 
                      2
                  ) e on e.gid = a.gid 
                group by 
                  1, 
                  2, 
                  3
              ) 
            group by 
              1, 
              2
          ) 
        group by 
          1
      ) c on a.C1 = c.C1 
    union all 
    Select 
      a.C1, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      'H' as ord, 
    from 
      (
        select 
          trial_status as C1, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M3 
        from 
          (
            select 
              trial_status, 
              month, 
              cast(active_users as float64)/ cast(days as float64) as Avg_active_users 
            from 
              (
                select 
                  trial_status, 
                  extract(
                    month 
                    from 
                      c_date
                  ) as month, 
                  count(*) as days, 
                  sum(user) as active_users 
                from 
                  (
                    select 
                      trial_status, 
                      c_date, 
                      count(*) as user 
                    from 
                      (
                        select 
                          trial_status, 
                          email, 
                          c_date 
                        from 
                          (
                            select 
                              gid, 
                              cast(
                                SUBSTR(
                                  CAST(
                                    datetime(__time, 'Asia/Kolkata') AS string
                                  ), 
                                  1, 
                                  10
                                ) as Date
                              ) as c_date, 
                              case when (
                                current_subscriber_status like '%Trial%'
                              ) then 'Free Trial Users' else 'Paid Active Users' end as trial_status 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and event_name = 'page_view' 
                              and (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              ) 
                              and (
                                lower(user_subscription_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%active%' 
                                or lower(user_subscription_status) like '%active%'
                              ) 
                            group by 
                              1, 
                              2, 
                              3
                          ) a 
                          inner join (
                            select 
                              g.gid, 
                              email 
                            from 
                              `et-poc-042021.all_user.gid_email` g 
                            group by 
                              1, 
                              2
                          ) e on e.gid = a.gid 
                        group by 
                          1, 
                          2, 
                          3
                      ) 
                    group by 
                      1, 
                      2
                  ) 
                group by 
                  1, 
                  2
              )
          ) 
        group by 
          1
      ) a 
      left join (
        select 
          trial_status as C1, 
          count(*) as yesterday 
        from 
          (
            select 
              trial_status, 
              email 
            from 
              (
                select 
                  gid, 
                  case when (
                    current_subscriber_status like '%Trial%'
                  ) then 'Free Trial Users' else 'Paid Active Users' end as trial_status 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-1 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and __time < cast(
                    current_date() as timestamp
                  ) 
                  and event_name = 'page_view' 
                  and (
                    url like '%articleshow%' 
                    or page_template like '%articleshow%'
                  ) 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                group by 
                  1, 
                  2
              ) a 
              inner join (
                select 
                  g.gid, 
                  email 
                from 
                  `et-poc-042021.all_user.gid_email` g 
                group by 
                  1, 
                  2
              ) e on e.gid = a.gid 
            group by 
              1, 
              2
          ) 
        group by 
          1
      ) b on a.C1 = b.C1 
      left join (
        select 
          trial_status as C1, 
          cast(
            sum(Active_users)/ 4 as int64
          ) as WOW_Avg 
        from 
          (
            select 
              trial_status, 
              c_date, 
              count(*) as Active_users 
            from 
              (
                select 
                  trial_status, 
                  email, 
                  c_date 
                from 
                  (
                    select 
                      gid, 
                      cast(
                        SUBSTR(
                          CAST(
                            datetime(__time, 'Asia/Kolkata') AS string
                          ), 
                          1, 
                          10
                        ) as Date
                      ) as c_date, 
                      case when (
                        current_subscriber_status like '%Trial%'
                      ) then 'Free Trial Users' else 'Paid Active Users' end as trial_status 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-29 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      ) 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                      and EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          date (
                            datetime(__time, 'Asia/Kolkata')
                          )
                      )= EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          current_date - 1
                      ) 
                    group by 
                      1, 
                      2, 
                      3
                  ) a 
                  inner join (
                    select 
                      g.gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.gid_email` g 
                    group by 
                      1, 
                      2
                  ) e on e.gid = a.gid 
                group by 
                  1, 
                  2, 
                  3
              ) 
            group by 
              1, 
              2
          ) 
        group by 
          1
      ) c on a.C1 = c.C1 
    Union all 
    Select 
      a.C1, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      concat(
        'I', 
        cast(O as string)
      ) as ord, 
    from 
      (
        select 
          fbuck as C1, 
          case when fbuck = 'Super User' then 1 when fbuck = 'Highly Engaged' then 2 when fbuck = 'Moderate' then 3 when fbuck = 'Occasional' then 4 when fbuck = 'Dormant' then 5 when fbuck = 'Expired' then 6 end as O, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then cast(
              Round(Avg_active_users) as int64
            ) else 0 end
          ) as M3 
        from 
          (
            select 
              fbuck, 
              month, 
              cast(active_users as float64)/ cast(days as float64) as Avg_active_users 
            from 
              (
                select 
                  fbuck, 
                  extract(
                    month 
                    from 
                      c_date
                  ) as month, 
                  count(*) as days, 
                  sum(user) as active_users 
                from 
                  (
                    select 
                      fbuck, 
                      c_date, 
                      count(*) as user 
                    from 
                      (
                        select 
                          case when fbuck is null then 'Expired' else fbuck end as fbuck, 
                          email, 
                          c_date 
                        from 
                          (
                            select 
                              gid, 
                              cast(
                                SUBSTR(
                                  CAST(
                                    datetime(__time, 'Asia/Kolkata') AS string
                                  ), 
                                  1, 
                                  10
                                ) as Date
                              ) as c_date 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and (
                                lower(user_subscription_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%active%' 
                                or lower(user_subscription_status) like '%active%'
                              ) 
                              and event_name = 'page_view' 
                              and (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              ) 
                            group by 
                              1, 
                              2
                          ) a 
                          inner join (
                            select 
                              fbuck, 
                              g.gid, 
                              g.email 
                            from 
                              `et-poc-042021.all_user.gid_email` g 
                              left join et - poc - 042021.Agg.Subscribed_RFV b on g.email = b.email 
                              and compute_date = cast(
                                current_date()-1 as string
                              ) 
                            group by 
                              1, 
                              2, 
                              3
                          ) e on e.gid = a.gid 
                        group by 
                          1, 
                          2, 
                          3
                      ) 
                    group by 
                      1, 
                      2
                  ) 
                group by 
                  1, 
                  2
              )
          ) 
        group by 
          1
      ) a 
      left join (
        select 
          fbuck as C1, 
          count(*) as yesterday 
        from 
          (
            select 
              fbuck, 
              email 
            from 
              (
                select 
                  gid, 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-1 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and __time < cast(
                    current_date() as timestamp
                  ) 
                  and event_name = 'page_view' 
                  and (
                    url like '%articleshow%' 
                    or page_template like '%articleshow%'
                  ) 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                group by 
                  1
              ) a 
              inner join (
                select 
                  g.gid, 
                  g.email, 
                  fbuck 
                from 
                  `et-poc-042021.all_user.gid_email` g 
                  left join et - poc - 042021.Agg.Subscribed_RFV b on g.email = b.email 
                  and compute_date = cast(
                    current_date()-1 as string
                  ) 
                group by 
                  1, 
                  2, 
                  3
              ) e on e.gid = a.gid 
            group by 
              1, 
              2
          ) 
        group by 
          1
      ) b on a.C1 = b.C1 
      left join (
        select 
          fbuck as C1, 
          cast(
            sum(Active_users)/ 4 as int64
          ) as WOW_Avg 
        from 
          (
            select 
              fbuck, 
              c_date, 
              count(*) as Active_users 
            from 
              (
                select 
                  case when fbuck is null then 'Expired' else fbuck end as fbuck, 
                  email, 
                  c_date 
                from 
                  (
                    select 
                      gid, 
                      cast(
                        SUBSTR(
                          CAST(
                            datetime(__time, 'Asia/Kolkata') AS string
                          ), 
                          1, 
                          10
                        ) as Date
                      ) as c_date 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-29 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      ) 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                      and EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          date (
                            datetime(__time, 'Asia/Kolkata')
                          )
                      )= EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          current_date - 1
                      ) 
                    group by 
                      1, 
                      2
                  ) a 
                  inner join (
                    select 
                      fbuck, 
                      g.gid, 
                      g.email 
                    from 
                      `et-poc-042021.all_user.gid_email` g 
                      left join et - poc - 042021.Agg.Subscribed_RFV b on g.email = b.email 
                      and compute_date = cast(
                        current_date()-1 as string
                      ) 
                    group by 
                      1, 
                      2, 
                      3
                  ) e on e.gid = a.gid 
                group by 
                  1, 
                  2, 
                  3
              ) 
            group by 
              1, 
              2
          ) 
        group by 
          1
      ) c on a.C1 = c.C1 
    Union all 
    select 
      a.nl_name, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      concat(
        'J', 
        cast(M1 as string)
      ) as ord 
    from 
      (
        select 
          nl_name, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then click_by_sent else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then click_by_sent else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then click_by_sent else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then click_by_sent else 0 end
          ) as M3 
        from 
          (
            select 
              nl_name, 
              month, 
              round(
                (
                  cast(mail_click as float64)/ cast(mail_sent as float64)
                )* 100, 
                1
              ) as click_by_sent 
            from 
              (
                select 
                  nl_name, 
                  extract(
                    month 
                    from 
                      c_date
                  ) as month, 
                  sum(mail_click) as mail_click, 
                  sum(mail_sent) as mail_sent 
                from 
                  (
                    select 
                      c_date, 
                      nl_name, 
                      sum(mail_click) as mail_click, 
                      count(*) as mail_sent 
                    from 
                      (
                        select 
                          email 
                        from 
                          (
                            select 
                              gid 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and event_name = 'page_view' 
                              and (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              ) 
                              and (
                                lower(user_subscription_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%active%' 
                                or lower(user_subscription_status) like '%active%'
                              ) 
                            group by 
                              1
                          ) a 
                          inner join (
                            select 
                              gid, 
                              email 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and gid is not null 
                              and email is not null 
                              and gid not in (
                                '00000000-0000-0000-0000-000000000000'
                              ) 
                            group by 
                              1, 
                              2
                          ) b on a.gid = b.gid 
                        group by 
                          1
                      ) a 
                      inner join (
                        select 
                          a.nl_name, 
                          a.c_date, 
                          a.email, 
                          case when b.email is null then 0 else 1 end as mail_click 
                        from 
                          (
                            select 
                              email, 
                              nl_name, 
                              nl_schedule_id, 
                              cast(
                                SUBSTR(
                                  CAST(
                                    datetime(__time, 'Asia/Kolkata') AS string
                                  ), 
                                  1, 
                                  10
                                ) as Date
                              ) as c_date 
                            FROM 
                              `et-poc-042021.all_user.EtClientEvents` 
                            WHERE 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and client_source = 'et_newsletter' 
                              and event_name in ('mail_sent') 
                              and nl_name not like '%Test%' 
                              and mailer_type in ('NEWSLETTER', 'PROMOTIONAL') 
                            group by 
                              1, 
                              2, 
                              3, 
                              4
                          ) a 
                          left join (
                            select 
                              email, 
                              nl_name, 
                              nl_schedule_id, 
                              cast(
                                SUBSTR(
                                  CAST(
                                    datetime(__time, 'Asia/Kolkata') AS string
                                  ), 
                                  1, 
                                  10
                                ) as Date
                              ) as c_date 
                            FROM 
                              `et-poc-042021.all_user.EtClientEvents` 
                            WHERE 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and client_source = 'et_newsletter' 
                              and event_name in ('mail_click') 
                              and nl_name not like '%Test%' 
                              and mailer_type in ('NEWSLETTER', 'PROMOTIONAL') 
                            group by 
                              1, 
                              2, 
                              3, 
                              4
                          ) b on a.nl_name = b.nl_name 
                          and a.nl_schedule_id = b.nl_schedule_id 
                          and a.email = b.email 
                          and b.c_date between a.c_date 
                          and a.c_date + 7 
                        group by 
                          1, 
                          2, 
                          3, 
                          4
                      ) e on e.email = a.email 
                    group by 
                      1, 
                      2 
                    having 
                      mail_sent > 0
                  ) 
                group by 
                  1, 
                  2
              )
          ) 
        group by 
          1
      ) a 
      left join (
        select 
          nl_name, 
          round(
            (
              cast(mail_click as float64)/ cast(mail_sent as float64)
            )* 100, 
            1
          ) as yesterday 
        from 
          (
            select 
              nl_name, 
              sum(mail_click) as mail_click, 
              count(*) as mail_sent 
            from 
              (
                select 
                  email 
                from 
                  (
                    select 
                      gid 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-30 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      ) 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                    group by 
                      1
                  ) a 
                  inner join (
                    select 
                      gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        current_date()-45 as timestamp
                      ) 
                      and gid is not null 
                      and email is not null 
                      and gid not in (
                        '00000000-0000-0000-0000-000000000000'
                      ) 
                    group by 
                      1, 
                      2
                  ) b on a.gid = b.gid 
                group by 
                  1
              ) a 
              inner join (
                select 
                  a.nl_name, 
                  a.email, 
                  case when b.email is null then 0 else 1 end as mail_click 
                from 
                  (
                    select 
                      email, 
                      nl_name, 
                      nl_schedule_id 
                    FROM 
                      `et-poc-042021.all_user.EtClientEvents` 
                    WHERE 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        current_date() as timestamp
                      ) 
                      and client_source = 'et_newsletter' 
                      and event_name in ('mail_sent') 
                      and nl_name not like '%Test%' 
                      and mailer_type in ('NEWSLETTER', 'PROMOTIONAL') 
                    group by 
                      1, 
                      2, 
                      3
                  ) a 
                  left join (
                    select 
                      email, 
                      nl_name, 
                      nl_schedule_id 
                    FROM 
                      `et-poc-042021.all_user.EtClientEvents` 
                    WHERE 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        current_date() as timestamp
                      ) 
                      and client_source = 'et_newsletter' 
                      and event_name in ('mail_click') 
                      and nl_name not like '%Test%' 
                      and mailer_type in ('NEWSLETTER', 'PROMOTIONAL') 
                    group by 
                      1, 
                      2, 
                      3
                  ) b on a.nl_name = b.nl_name 
                  and a.nl_schedule_id = b.nl_schedule_id 
                  and a.email = b.email
              ) e on e.email = a.email 
            group by 
              1 
            having 
              mail_sent > 0
          )
      ) b on a.nl_name = b.nl_name 
      left join (
        select 
          nl_name, 
          round(
            (
              cast(mail_click as float64)/ cast(mail_sent as float64)
            )* 100, 
            1
          ) as WOW_Avg 
        from 
          (
            select 
              nl_name, 
              sum(mail_click)/ 4 as mail_click, 
              count(*)/ 4 as mail_sent 
            from 
              (
                select 
                  email 
                from 
                  (
                    select 
                      gid 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-30 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      ) 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                    group by 
                      1
                  ) a 
                  inner join (
                    select 
                      gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        current_date()-45 as timestamp
                      ) 
                      and gid is not null 
                      and email is not null 
                      and gid not in (
                        '00000000-0000-0000-0000-000000000000'
                      ) 
                    group by 
                      1, 
                      2
                  ) b on a.gid = b.gid 
                group by 
                  1
              ) a 
              inner join (
                select 
                  a.nl_name, 
                  a.c_date, 
                  a.email, 
                  case when b.email is null then 0 else 1 end as mail_click 
                from 
                  (
                    select 
                      email, 
                      nl_name, 
                      nl_schedule_id, 
                      cast(
                        SUBSTR(
                          CAST(
                            datetime(__time, 'Asia/Kolkata') AS string
                          ), 
                          1, 
                          10
                        ) as Date
                      ) as c_date 
                    FROM 
                      `et-poc-042021.all_user.EtClientEvents` 
                    WHERE 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-29 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and client_source = 'et_newsletter' 
                      and event_name in ('mail_sent') 
                      and EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          date (
                            datetime(__time, 'Asia/Kolkata')
                          )
                      )= EXTRACT(
                        DAYOFWEEK 
                        FROM 
                          current_date - 1
                      ) 
                      and nl_name not like '%Test%' 
                      and mailer_type in ('NEWSLETTER', 'PROMOTIONAL') 
                    group by 
                      1, 
                      2, 
                      3, 
                      4
                  ) a 
                  left join (
                    select 
                      email, 
                      nl_name, 
                      nl_schedule_id, 
                      cast(
                        SUBSTR(
                          CAST(
                            datetime(__time, 'Asia/Kolkata') AS string
                          ), 
                          1, 
                          10
                        ) as Date
                      ) as c_date 
                    FROM 
                      `et-poc-042021.all_user.EtClientEvents` 
                    WHERE 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-29 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and __time < cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-1 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and client_source = 'et_newsletter' 
                      and event_name in ('mail_click') 
                      and nl_name not like '%Test%' 
                      and mailer_type in ('NEWSLETTER', 'PROMOTIONAL') 
                    group by 
                      1, 
                      2, 
                      3, 
                      4
                  ) b on a.nl_name = b.nl_name 
                  and a.nl_schedule_id = b.nl_schedule_id 
                  and a.email = b.email 
                  and b.c_date between a.c_date 
                  and a.c_date + 7 
                group by 
                  1, 
                  2, 
                  3, 
                  4
              ) e on e.email = a.email 
            group by 
              1 
            having 
              mail_sent > 0
          )
      ) c on a.nl_name = c.nl_name 
    where 
      WOW_Avg > 0 
      and M1 > 0 
      and M2 > 0 
    Union all 
    select 
      a.nl_name, 
      yesterday, 
      WOW_Avg, 
      MTD, 
      M1, 
      M2, 
      M3, 
      'K' as ord 
    from 
      (
        select 
          nl_name, 
          sum(
            case when month = extract(
              month 
              from 
                current_date()
            ) then cast(
              cast(mail_click as float64)/ cast(days as float64) as int64
            ) else 0 end
          ) as MTD, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 1 MONTH
                )
            ) then cast(
              cast(mail_click as float64)/ cast(days as float64) as int64
            ) else 0 end
          ) as M1, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 2 MONTH
                )
            ) then cast(
              cast(mail_click as float64)/ cast(days as float64) as int64
            ) else 0 end
          ) as M2, 
          sum(
            case when month = extract(
              month 
              from 
                DATE_SUB(
                  Date(
                    extract(
                      year 
                      from 
                        current_date()
                    ), 
                    extract(
                      month 
                      from 
                        current_date()
                    ), 
                    01
                  ), 
                  INTERVAL 3 MONTH
                )
            ) then cast(
              cast(mail_click as float64)/ cast(days as float64) as int64
            ) else 0 end
          ) as M3 
        from 
          (
            select 
              'user_visited_from_newsletter' as nl_name, 
              extract(
                month 
                from 
                  c_date
              ) as month, 
              sum(mail_click) as mail_click, 
              count(*) as days 
            from 
              (
                select 
                  c_date, 
                  count(*) as mail_click 
                from 
                  (
                    select 
                      c_date, 
                      e.email 
                    from 
                      (
                        select 
                          email 
                        from 
                          (
                            select 
                              gid 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and event_name = 'page_view' 
                              and (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              ) 
                              and (
                                lower(user_subscription_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%paid%' 
                                or lower(current_subscriber_status) like '%active%' 
                                or lower(user_subscription_status) like '%active%'
                              ) 
                            group by 
                              1
                          ) a 
                          inner join (
                            select 
                              gid, 
                              email 
                            from 
                              `et-poc-042021.all_user.EtClientEvents` 
                            where 
                              __time >= cast(
                                DATETIME_SUB(
                                  DATETIME_SUB(
                                    cast (
                                      DATE_SUB(
                                        Date(
                                          extract(
                                            year 
                                            from 
                                              current_date()
                                          ), 
                                          extract(
                                            month 
                                            from 
                                              current_date()
                                          ), 
                                          01
                                        ), 
                                        INTERVAL 3 MONTH
                                      ) as timestamp
                                    ), 
                                    INTERVAL 5 Hour
                                  ), 
                                  INTERVAL 30 minute
                                ) as timestamp
                              ) 
                              and __time < cast(
                                current_date() as timestamp
                              ) 
                              and gid is not null 
                              and email is not null 
                              and gid not in (
                                '00000000-0000-0000-0000-000000000000'
                              ) 
                            group by 
                              1, 
                              2
                          ) b on a.gid = b.gid 
                        group by 
                          1
                      ) a 
                      inner join (
                        select 
                          email, 
                          cast(
                            SUBSTR(
                              CAST(
                                datetime(__time, 'Asia/Kolkata') AS string
                              ), 
                              1, 
                              10
                            ) as Date
                          ) as c_date 
                        FROM 
                          `et-poc-042021.all_user.EtClientEvents` 
                        WHERE 
                          __time >= cast(
                            DATETIME_SUB(
                              DATETIME_SUB(
                                cast (
                                  DATE_SUB(
                                    Date(
                                      extract(
                                        year 
                                        from 
                                          current_date()
                                      ), 
                                      extract(
                                        month 
                                        from 
                                          current_date()
                                      ), 
                                      01
                                    ), 
                                    INTERVAL 3 MONTH
                                  ) as timestamp
                                ), 
                                INTERVAL 5 Hour
                              ), 
                              INTERVAL 30 minute
                            ) as timestamp
                          ) 
                          and __time < cast(
                            current_date() as timestamp
                          ) 
                          and client_source = 'et_newsletter' 
                          and event_name in ('mail_click') 
                          and nl_name not like '%Test%' 
                          and mailer_type in ('NEWSLETTER', 'PROMOTIONAL') 
                        group by 
                          1, 
                          2
                      ) e on e.email = a.email 
                    group by 
                      1, 
                      2
                  ) 
                group by 
                  1
              ) 
            group by 
              1, 
              2
          ) 
        group by 
          1
      ) a 
      left join (
        select 
          'user_visited_from_newsletter' as nl_name, 
          count(*) as yesterday 
        from 
          (
            select 
              e.email 
            from 
              (
                select 
                  email 
                from 
                  (
                    select 
                      gid 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-30 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      ) 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                    group by 
                      1
                  ) a 
                  inner join (
                    select 
                      gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        current_date()-45 as timestamp
                      ) 
                      and gid is not null 
                      and email is not null 
                      and gid not in (
                        '00000000-0000-0000-0000-000000000000'
                      ) 
                    group by 
                      1, 
                      2
                  ) b on a.gid = b.gid 
                group by 
                  1
              ) a 
              inner join (
                select 
                  email 
                FROM 
                  `et-poc-042021.all_user.EtClientEvents` 
                WHERE 
                  __time >= cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-1 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and __time < cast(
                    current_date() as timestamp
                  ) 
                  and client_source = 'et_newsletter' 
                  and event_name in ('mail_click') 
                  and nl_name not like '%Test%' 
                  and mailer_type in ('NEWSLETTER', 'PROMOTIONAL') 
                group by 
                  1
              ) e on e.email = a.email 
            group by 
              1
          )
      ) b on a.nl_name = b.nl_name 
      left join (
        select 
          'user_visited_from_newsletter' as nl_name, 
          count(*)/ 4 as WOW_Avg 
        from 
          (
            select 
              e.email 
            from 
              (
                select 
                  email 
                from 
                  (
                    select 
                      gid 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        DATETIME_SUB(
                          DATETIME_SUB(
                            cast(
                              CURRENT_DATE()-30 as datetime
                            ), 
                            INTERVAL 5 HOUR
                          ), 
                          INTERVAL 30 Minute
                        ) as timestamp
                      ) 
                      and event_name = 'page_view' 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      ) 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                    group by 
                      1
                  ) a 
                  inner join (
                    select 
                      gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= cast(
                        current_date()-45 as timestamp
                      ) 
                      and gid is not null 
                      and email is not null 
                      and gid not in (
                        '00000000-0000-0000-0000-000000000000'
                      ) 
                    group by 
                      1, 
                      2
                  ) b on a.gid = b.gid 
                group by 
                  1
              ) a 
              inner join (
                select 
                  email, 
                  cast(
                    SUBSTR(
                      CAST(
                        datetime(__time, 'Asia/Kolkata') AS string
                      ), 
                      1, 
                      10
                    ) as Date
                  ) as c_date 
                FROM 
                  `et-poc-042021.all_user.EtClientEvents` 
                WHERE 
                  __time >= cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-29 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and __time < cast(
                    DATETIME_SUB(
                      DATETIME_SUB(
                        cast(
                          CURRENT_DATE()-1 as datetime
                        ), 
                        INTERVAL 5 HOUR
                      ), 
                      INTERVAL 30 Minute
                    ) as timestamp
                  ) 
                  and client_source = 'et_newsletter' 
                  and event_name in ('mail_click') 
                  and EXTRACT(
                    DAYOFWEEK 
                    FROM 
                      date (
                        datetime(__time, 'Asia/Kolkata')
                      )
                  )= EXTRACT(
                    DAYOFWEEK 
                    FROM 
                      current_date - 1
                  ) 
                  and nl_name not like '%Test%' 
                  and mailer_type in ('NEWSLETTER', 'PROMOTIONAL') 
                group by 
                  1, 
                  2
              ) e on e.email = a.email 
            group by 
              1
          )
      ) c on a.nl_name = c.nl_name
  ) 
order by 
  ord asc

~~~
## Subscriptions:

## About: This report is used to analyze both the B2B and B2C Subscriptions number based on different platform, different plans, on different methods like retargeting on daily, weekly, monthly and quarterly level

### Query-1:
~~~
SELECT 
  * 
FROM 
  (
    SELECT 
      clmn8_, 
      clmn5_, 
      clmn100001_, 
      COUNT(1) AS clmn100002_ 
    FROM 
      (
        SELECT 
          * 
        FROM 
          (
            SELECT 
              clmn8_, 
              clmn5_, 
              clmn2_, 
              CASE WHEN clmn2_ IN (1, 12, 43, 42, 62) THEN "Monthly" WHEN clmn2_ IN (3, 14, 30, 40, 33, 38, 41, 51, 52, 59) THEN "Annual" WHEN clmn2_ IN (8, 39, 50) THEN "2-Year" WHEN clmn2_ IN (36) THEN "3-Year" WHEN clmn2_ IN (2, 13, 35, 49, 60) THEN "Quarterly" ELSE CAST(NULL AS STRING) END AS clmn100000_, 
              CASE WHEN clmn2_ IN (1, 12, 43, 42, 62) THEN "Monthly" WHEN clmn2_ IN (3, 14, 30, 40, 33, 38, 41, 51, 52, 59) THEN "Annual" WHEN clmn2_ IN (8, 39, 50) THEN "2-Year" WHEN clmn2_ IN (36) THEN "3-Year" WHEN clmn2_ IN (2, 13, 35, 49, 60) THEN "Quarterly" ELSE CAST(NULL AS STRING) END AS clmn100001_ 
            FROM 
              (
                SELECT 
                  t0.Ndate AS clmn5_, 
                  t0.auto_renew AS clmn8_, 
                  t0.plan_id AS clmn2_ 
                FROM 
                  (
                    select 
                      * 
                    from 
                      (
                        select 
                          _id, 
                          A.auto_renew, 
                          A.user_subscription_account_id, 
                          plan_id, 
                          trial_avalability, 
                          platform, 
                          Ndate, 
                          B.Status, 
                          ROW_NUMBER() over(
                            partition by _id 
                            order by 
                              Status
                          ) rank 
                        from 
                          (
                            select 
                              _id, 
                              auto_renew, 
                              user_subscription_account_id, 
                              plan_id, 
                              trial_avalability, 
                              platform, 
                              cast(
                                DATETIME_ADD(
                                  DATETIME_ADD(
                                    cast(payment_success_date as datetime), 
                                    INTERVAL 5 HOUR
                                  ), 
                                  INTERVAL 30 Minute
                                ) as date
                              ) Ndate 
                            from 
                              et - poc - 042021.all_user.billing_transactions 
                            where 
                              bill_transaction_source in (
                                'ETPAY', 'APPSTORE', 'PLAYSTORE', 
                                'SWG'
                              ) 
                              and bill_transanction_type = 'BUY' 
                              and bill_transaction_payment_state = 'PAYMENT_SUCCESS'
                          ) A 
                          left join (
                            select 
                              user_subscription_account_id, 
                              graceenddate, 
                              case when date_diff(trialenddate, startdate, DAY) <= 18 then "Trail Exp" else "Sub Exp" end As Status 
                            from 
                              (
                                select 
                                  user_subscription_account_id, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_subscriptionStartDate as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) startdate, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_subscriptionEndDateFromPayment as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) enddate, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_subscriptionTrialEndDate as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) trialenddate, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_renewWindowCloseDate as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) graceenddate 
                                from 
                                  `et-poc-042021.all_user.user_subscriptions` 
                                where 
                                  user_billing_transaction_bill_transaction_source in(
                                    'ETPAY', 'APPSTORE', 'PLAYSTORE', 
                                    'SWG', 'OFFLINE'
                                  ) 
                                  and subscription_status in ('EXPIRED')
                              )
                          ) B on A.user_subscription_account_id = B.user_subscription_account_id 
                          and A.Ndate > B.graceenddate 
                        where 
                          Ndate > '2021-01-01'
                      ) 
                    where 
                      rank = 1
                  ) t0
              )
          ) 
        WHERE 
          (
            (clmn5_ >= DATE "2022-04-18") 
            AND (clmn5_ <= DATE "2022-04-24") 
            AND (clmn2_ <> 5) 
            AND (
              NOT (clmn100000_ IS NULL)
            )
          )
      ) 
    GROUP BY 
      clmn8_, 
      clmn5_, 
      clmn100001_
  ) 
LIMIT 
  20000000

~~~
### Query-2
~~~
SELECT 
  * 
FROM 
  (
    SELECT 
      clmn5_, 
      COUNT(1) AS clmn100001_ 
    FROM 
      (
        SELECT 
          * 
        FROM 
          (
            SELECT 
              clmn5_, 
              clmn2_, 
              CASE WHEN clmn2_ IN (1, 12, 43, 42, 62) THEN "Monthly" WHEN clmn2_ IN (3, 14, 30, 40, 33, 38, 41, 51, 52, 59) THEN "Annual" WHEN clmn2_ IN (8, 39, 50) THEN "2-Year" WHEN clmn2_ IN (36) THEN "3-Year" WHEN clmn2_ IN (2, 13, 35, 49, 60) THEN "Quarterly" ELSE CAST(NULL AS STRING) END AS clmn100000_ 
            FROM 
              (
                SELECT 
                  t0.Ndate AS clmn5_, 
                  t0.plan_id AS clmn2_ 
                FROM 
                  (
                    select 
                      * 
                    from 
                      (
                        select 
                          _id, 
                          A.auto_renew, 
                          A.user_subscription_account_id, 
                          plan_id, 
                          trial_avalability, 
                          platform, 
                          Ndate, 
                          B.Status, 
                          ROW_NUMBER() over(
                            partition by _id 
                            order by 
                              Status
                          ) rank 
                        from 
                          (
                            select 
                              _id, 
                              auto_renew, 
                              user_subscription_account_id, 
                              plan_id, 
                              trial_avalability, 
                              platform, 
                              cast(
                                DATETIME_ADD(
                                  DATETIME_ADD(
                                    cast(payment_success_date as datetime), 
                                    INTERVAL 5 HOUR
                                  ), 
                                  INTERVAL 30 Minute
                                ) as date
                              ) Ndate 
                            from 
                              et - poc - 042021.all_user.billing_transactions 
                            where 
                              bill_transaction_source in (
                                'ETPAY', 'APPSTORE', 'PLAYSTORE', 
                                'SWG'
                              ) 
                              and bill_transanction_type = 'BUY' 
                              and bill_transaction_payment_state = 'PAYMENT_SUCCESS'
                          ) A 
                          left join (
                            select 
                              user_subscription_account_id, 
                              graceenddate, 
                              case when date_diff(trialenddate, startdate, DAY) <= 18 then "Trail Exp" else "Sub Exp" end As Status 
                            from 
                              (
                                select 
                                  user_subscription_account_id, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_subscriptionStartDate as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) startdate, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_subscriptionEndDateFromPayment as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) enddate, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_subscriptionTrialEndDate as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) trialenddate, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_renewWindowCloseDate as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) graceenddate 
                                from 
                                  `et-poc-042021.all_user.user_subscriptions` 
                                where 
                                  user_billing_transaction_bill_transaction_source in(
                                    'ETPAY', 'APPSTORE', 'PLAYSTORE', 
                                    'SWG', 'OFFLINE'
                                  ) 
                                  and subscription_status in ('EXPIRED')
                              )
                          ) B on A.user_subscription_account_id = B.user_subscription_account_id 
                          and A.Ndate > B.graceenddate 
                        where 
                          Ndate > '2021-01-01'
                      ) 
                    where 
                      rank = 1
                  ) t0
              )
          ) 
        WHERE 
          (
            (clmn5_ >= DATE "2022-04-18") 
            AND (clmn5_ <= DATE "2022-04-24") 
            AND (clmn2_ <> 5) 
            AND (
              NOT (clmn100000_ IS NULL)
            )
          )
      ) 
    GROUP BY 
      clmn5_
  ) 
LIMIT 
  20000000

~~~

### Query-3:
~~~
SELECT 
  * 
FROM 
  (
    SELECT 
      clmn8_, 
      clmn5_, 
      clmn4_, 
      COUNT(1) AS clmn100001_ 
    FROM 
      (
        SELECT 
          * 
        FROM 
          (
            SELECT 
              clmn8_, 
              clmn5_, 
              clmn2_, 
              clmn4_, 
              CASE WHEN clmn2_ IN (1, 12, 43, 42, 62) THEN "Monthly" WHEN clmn2_ IN (3, 14, 30, 40, 33, 38, 41, 51, 52, 59) THEN "Annual" WHEN clmn2_ IN (8, 39, 50) THEN "2-Year" WHEN clmn2_ IN (36) THEN "3-Year" WHEN clmn2_ IN (2, 13, 35, 49, 60) THEN "Quarterly" ELSE CAST(NULL AS STRING) END AS clmn100000_ 
            FROM 
              (
                SELECT 
                  t0.Ndate AS clmn5_, 
                  t0.auto_renew AS clmn8_, 
                  t0.plan_id AS clmn2_, 
                  t0.platform AS clmn4_ 
                FROM 
                  (
                    select 
                      * 
                    from 
                      (
                        select 
                          _id, 
                          A.auto_renew, 
                          A.user_subscription_account_id, 
                          plan_id, 
                          trial_avalability, 
                          platform, 
                          Ndate, 
                          B.Status, 
                          ROW_NUMBER() over(
                            partition by _id 
                            order by 
                              Status
                          ) rank 
                        from 
                          (
                            select 
                              _id, 
                              auto_renew, 
                              user_subscription_account_id, 
                              plan_id, 
                              trial_avalability, 
                              platform, 
                              cast(
                                DATETIME_ADD(
                                  DATETIME_ADD(
                                    cast(payment_success_date as datetime), 
                                    INTERVAL 5 HOUR
                                  ), 
                                  INTERVAL 30 Minute
                                ) as date
                              ) Ndate 
                            from 
                              et - poc - 042021.all_user.billing_transactions 
                            where 
                              bill_transaction_source in (
                                'ETPAY', 'APPSTORE', 'PLAYSTORE', 
                                'SWG'
                              ) 
                              and bill_transanction_type = 'BUY' 
                              and bill_transaction_payment_state = 'PAYMENT_SUCCESS'
                          ) A 
                          left join (
                            select 
                              user_subscription_account_id, 
                              graceenddate, 
                              case when date_diff(trialenddate, startdate, DAY) <= 18 then "Trail Exp" else "Sub Exp" end As Status 
                            from 
                              (
                                select 
                                  user_subscription_account_id, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_subscriptionStartDate as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) startdate, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_subscriptionEndDateFromPayment as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) enddate, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_subscriptionTrialEndDate as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) trialenddate, 
                                  cast(
                                    DATETIME_ADD(
                                      DATETIME_ADD(
                                        cast(
                                          plan_expiration_details_0_renewWindowCloseDate as datetime
                                        ), 
                                        INTERVAL 5 HOUR
                                      ), 
                                      INTERVAL 30 Minute
                                    ) as date
                                  ) graceenddate 
                                from 
                                  `et-poc-042021.all_user.user_subscriptions` 
                                where 
                                  user_billing_transaction_bill_transaction_source in(
                                    'ETPAY', 'APPSTORE', 'PLAYSTORE', 
                                    'SWG', 'OFFLINE'
                                  ) 
                                  and subscription_status in ('EXPIRED')
                              )
                          ) B on A.user_subscription_account_id = B.user_subscription_account_id 
                          and A.Ndate > B.graceenddate 
                        where 
                          Ndate > '2021-01-01'
                      ) 
                    where 
                      rank = 1
                  ) t0
              )
          ) 
        WHERE 
          (
            (clmn5_ >= DATE "2022-04-18") 
            AND (clmn5_ <= DATE "2022-04-24") 
            AND (clmn2_ <> 5) 
            AND (
              NOT (clmn100000_ IS NULL)
            )
          )
      ) 
    GROUP BY 
      clmn8_, 
      clmn5_, 
      clmn4_
  ) 
LIMIT 
  20000000

~~~
