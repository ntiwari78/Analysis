- [Adhoc Query](#adhoc-query)
  * [Query-1 Objective - To find the engagement of users who subscribed in jan](#query-1--objective--to-find-the-engagement-of-users-who-subscribed-in-jan)
  * [Query-2: Objective: To find the Newsletter engagement  and section wise engagement of users who subscribed in jan](#query-2--objective--to-find-the-newsletter-engagement--and-section-wise-engagement-of-users-who-subscribed-in-jan)
  * [Query-3: Objective: Day Wise Engagement of yearly plan paid users in March 2021](#query-3--objective--day-wise-engagement-of-yearly-plan-paid-users-in-march-2021)
  * [Query-4: Objective: Query is used to analyze what behavior of users help to increase propensity to convert on different metrics:](#query-4--objective--query-is-used-to-analyze-what-behavior-of-users-help-to-increase-propensity-to-convert-on-different-metrics-)
  * [Query-5: Objective: To find the daywise users on mweb unique based on there email and gid](#query-5--objective--to-find-the-daywise-users-on-mweb-unique-based-on-there-email-and-gid)
  * [Query-6: Objective: Queries used to find  list of users with email and phone number who left ET being free loyal users from Dec till march](#query-6--objective--queries-used-to-find--list-of-users-with-email-and-phone-number-who-left-et-being-free-loyal-users-from-dec-till-march)
  * [Query-7: Objective: Queries used to find list of free loyal retained users from Dec 21 to Mar 22](#query-7--objective--queries-used-to-find-list-of-free-loyal-retained-users-from-dec-21-to-mar-22)
  * [Query-8: Objective: Queries used to Analyze the movement of users from one user engagement bucket to another in different months](#query-8--objective--queries-used-to-analyze-the-movement-of-users-from-one-user-engagement-bucket-to-another-in-different-months)
  * [Query-9: Objective: Query used to generate the links which is used on different pages on  desktop which in return increase the visibility of pages on web browsers](#query-9--objective--query-used-to-generate-the-links-which-is-used-on-different-pages-on--desktop-which-in-return-increase-the-visibility-of-pages-on-web-browsers)
  * [Final Query for interlinking data](#final-query-for-interlinking-data)
  * [Query-10: Objective:To Analyze the user’s watchlist and portfolio addition behavior](#query-10--objective-to-analyze-the-user-s-watchlist-and-portfolio-addition-behavior)
  * [Query-11: Objective: To Analyze the behavior of loyal free users based on city](#query-11--objective--to-analyze-the-behavior-of-loyal-free-users-based-on-city)
  * [Query-12: Objective:To Analyze the behavior of loyal free users based on platform wise](#query-12--objective-to-analyze-the-behavior-of-loyal-free-users-based-on-platform-wise)
  * [Query-13: Objective: To Analyze the behavior of loyal free users based on Source wise](#query-13--objective--to-analyze-the-behavior-of-loyal-free-users-based-on-source-wise)
  * [Query-14: Objective: To Analyze the behavior of loyal free users based on Site section wise excluding app](#query-14--objective--to-analyze-the-behavior-of-loyal-free-users-based-on-site-section-wise-excluding-app)
  * [Query-15: Objective: To Analyze the behavior of loyal free users based on Page Template wise excluding app](#query-15--objective--to-analyze-the-behavior-of-loyal-free-users-based-on-page-template-wise-excluding-app)
  * [Query-16: Objective: To Analyze the behavior of loyal free users based on there Events](#query-16--objective--to-analyze-the-behavior-of-loyal-free-users-based-on-there-events)
  * [Query-17: Objective: To Analyze the behavior of loyal free users based on there Recency, frequency and Volume](#query-17--objective--to-analyze-the-behavior-of-loyal-free-users-based-on-there-recency--frequency-and-volume)
  * [Query-18: Objective: To Choose users who should be given free subscription which in return help us in engagement increasing loyalty and also would not target our subscription revenue](#query-18--objective--to-choose-users-who-should-be-given-free-subscription-which-in-return-help-us-in-engagement-increasing-loyalty-and-also-would-not-target-our-subscription-revenue)
  * [Query-19: Objective: Timesprime expired users analysis](#query-19--objective--timesprime-expired-users-analysis)
  * [Query-20: Objective: Queries to analyze the impact of migration of portfolio db to follow db](#query-20--objective--queries-to-analyze-the-impact-of-migration-of-portfolio-db-to-follow-db)
  * [Query-21: Objective: Query Used to analyze the traffic on homepage and how many free and paid users are reading articles from home page](#query-21--objective--query-used-to-analyze-the-traffic-on-homepage-and-how-many-free-and-paid-users-are-reading-articles-from-home-page)
  * [Query-22: Objective: Query Used to analyze engagement of paid users on App](#query-22--objective--query-used-to-analyze-engagement-of-paid-users-on-app)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

## Query-1 Objective - To find the engagement of users who subscribed in jan 
~~~
select 
  a.c_date, 
  user_email, 
  payment_success_date, 
  subscription_start_date, 
  subscription_end_date, 
  Active_on_day, 
  Active_desktop, 
  Active_mweb, 
  Active_android, 
  Active_ios, 
  coalesce(Free_pv, 0) as free_pv, 
  coalesce(prime_pv, 0) as prime_pv, 
  coalesce(premium_pv, 0) as premium_pv, 
  coalesce(Free_pv, 0)+ coalesce(prime_pv, 0)+ coalesce(premium_pv, 0) as Total_articles_read_pv, 
  desktop_stories, 
  mweb_stories, 
  android_stories, 
  ios_stories, 
  Total_stories, 
  coalesce(Free_pv, 0)+ coalesce(prime_pv, 0)+ coalesce(premium_pv, 0)+ coalesce(company, 0)+ coalesce(video, 0)+ coalesce(commodity, 0)+ coalesce(slideshow, 0)+ coalesce(stock_report, 0) as Total_pv 
from 
  (
    select 
      user_email, 
      cast(
        DATETIME_ADD(
          DATETIME_ADD(
            cast(payment_success_date as datetime), 
            INTERVAL 5 HOUR
          ), 
          INTERVAL 30 Minute
        ) as date
      ) as payment_success_date, 
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
      ) as subscription_start_date, 
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
      ) as subscription_end_date 
    from 
      (
        SELECT 
          a.user_subscription_account_id, 
          max(payment_success_date) as payment_success_date, 
          max(
            plan_expiration_details_0_subscriptionStartDate
          ) as plan_expiration_details_0_subscriptionStartDate, 
          max(
            plan_expiration_details_0_subscriptionEndDateFromPayment
          ) as plan_expiration_details_0_subscriptionEndDateFromPayment 
        FROM 
          et - poc - 042021.all_user.billing_transactions a 
          left join all_user.user_subscriptions b on a.user_subscription_account_id = b.user_subscription_account_id 
        where 
          user_billing_transaction_plan_id not in (7, 6, 25) 
          and a.plan_name = 'Monthly Membership' 
          and user_billing_transaction_bill_transaction_source not in ('OFFLINE') 
          and bill_transaction_source != 'OFFLINE' 
          and bill_transaction_state = 'TRANSACTION_SUCCESS' 
          and a.plan_code not in ('author_plan', '1article') 
          and payment_success_date > '2021-12-31T18:30:00Z' 
          and payment_success_date < '2022-01-31T18:30:00Z' 
          and a.product_code in ('ETPR') 
        group by 
          1
      ) a 
      inner join et - poc - 042021.all_user.user_subscription_accounts b on a.user_subscription_account_id = SUBSTR(
        SUBSTR(b._id, 10), 
        1, 
        LENGTH(
          SUBSTR(b._id, 10)
        )-1
      ) 
    group by 
      1, 
      2, 
      3, 
      4
  ) s 
  left join (
    select 
      email, 
      c_date, 
      Active_desktop, 
      Active_mweb, 
      Active_android, 
      Active_ios, 
      case when Active_desktop + Active_mweb + Active_android + Active_ios >= 1 then 1 else 0 end as Active_on_day 
    from 
      (
        select 
          email, 
          c_date, 
          sum(
            case when platform = 'desktop' then 1 else 0 end
          ) as Active_desktop, 
          sum(
            case when platform = 'mweb' then 1 else 0 end
          ) as Active_mweb, 
          sum(
            case when platform = 'android' then 1 else 0 end
          ) as Active_android, 
          sum(
            case when platform = 'ios' then 1 else 0 end
          ) as Active_ios 
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
                  ) then 'mweb' else lower(platform) end as platform, 
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
                  __time >= '2021-12-31 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
                  and client_source = 'growth_rx' 
                  and (
                    (
                      event_name = 'page_view' 
                      and (
                        (
                          url like '%companyid%' 
                          or (
                            url like '%/company/%' 
                            and url not like '%articleshow%' 
                            and platform in ('ios', 'android')
                          )
                        ) 
                        or (
                          (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and et_product not in (
                            'Behind Sign In', 'Advertorial', 
                            'homepage'
                          )
                        ) 
                        or (
                          (page_template like '%commodity%') 
                          or (url like '%commodity%')
                        ) 
                        or (url like '%slideshow%') 
                        or (
                          page_template = 'Stock Report Viewer' 
                          and et_product in ('Stock Report')
                        ) 
                        or (url like '%videoshow%')
                      )
                    ) 
                    or (
                      event_category in (
                        'Premium Articles PV Tracking', 
                        'Articleshow PV Tracking'
                      )
                    )
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
                  __time >= '2021-12-31 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
                  and gid not in (
                    '00000000-0000-0000-0000-000000000000'
                  ) 
                  and gid is not null 
                  and email is not null 
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
  ) a on s.user_email = a.email 
  left join (
    select 
      c_date, 
      email, 
      sum(
        case when et_product = 'Premium' then pv else 0 end
      ) as premium_pv, 
      sum(
        case when et_product = 'Prime Plus' then pv else 0 end
      ) as prime_pv, 
      sum(
        case when et_product = 'Free' then pv else 0 end
      ) as Free_pv 
    from 
      (
        select 
          c_date, 
          email, 
          et_product, 
          sum(pv) as pv 
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
              gid, 
              case when et_product = 'Premium' then 'Premium' when et_product = 'Prime Plus' then 'Prime Plus' else 'Free' end as et_product, 
              msid, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                (
                  (
                    url like '%articleshow%' 
                    or page_template like '%articleshow%'
                  ) 
                  and event_name = 'page_view' 
                  and et_product not in (
                    'Behind Sign In', 'Advertorial', 
                    'homepage'
                  )
                ) 
                or (
                  event_category in (
                    'Premium Articles PV Tracking', 
                    'Articleshow PV Tracking'
                  )
                )
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
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) b on a.email = b.email 
  and a.c_date = b.c_date 
  left join (
    select 
      c_date, 
      email, 
      desktop_stories, 
      mweb_stories, 
      android_stories, 
      ios_stories, 
      desktop_stories + mweb_stories + android_stories + ios_stories as Total_stories 
    from 
      (
        select 
          c_date, 
          email, 
          sum(
            case when platform = 'desktop' then article_read else 0 end
          ) as desktop_stories, 
          sum(
            case when platform = 'mweb' then article_read else 0 end
          ) as mweb_stories, 
          sum(
            case when platform = 'android' then article_read else 0 end
          ) as android_stories, 
          sum(
            case when platform = 'ios' then article_read else 0 end
          ) as ios_stories 
        from 
          (
            select 
              c_date, 
              email, 
              platform, 
              count(*) as article_read 
            from 
              (
                select 
                  c_date, 
                  email, 
                  platform, 
                  msid 
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
                      gid, 
                      case when (
                        (
                          platform = 'web' 
                          and device_category = 'mobile'
                        ) 
                        or platform in ('amp', 'pwa', 'PWA', 'AMP', 'mweb')
                      ) then 'mweb' else lower(platform) end as platform, 
                      msid 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= '2021-12-31 18:30:00' 
                      and __time < '2022-03-31 18:30:00' 
                      and (
                        (
                          (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and event_name = 'page_view' 
                          and et_product not in (
                            'Behind Sign In', 'Advertorial', 
                            'homepage'
                          )
                        ) 
                        or (
                          event_category in (
                            'Premium Articles PV Tracking', 
                            'Articleshow PV Tracking'
                          )
                        )
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
                      __time >= '2021-12-31 18:30:00' 
                      and __time < '2022-03-31 18:30:00' 
                      and gid not in (
                        '00000000-0000-0000-0000-000000000000'
                      ) 
                      and gid is not null 
                      and email is not null 
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
              2, 
              3
          ) 
        group by 
          1, 
          2
      )
  ) c on a.email = c.email 
  and a.c_date = c.c_date 
  left join (
    select 
      c_date, 
      email, 
      sum(pv) as company 
    from 
      (
        select 
          c_date, 
          email, 
          url, 
          sum(pv) as pv 
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
              gid, 
              url, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                (
                  url like '%companyid%' 
                  or (
                    url like '%/company/%' 
                    and url not like '%articleshow%' 
                    and platform in ('ios', 'android')
                  )
                ) 
                and event_name = 'page_view'
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
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) ac on a.c_date = ac.c_date 
  and a.email = ac.email 
  left join (
    select 
      c_date, 
      email, 
      sum(pv) as video 
    from 
      (
        select 
          c_date, 
          email, 
          url, 
          sum(pv) as pv 
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
              gid, 
              url, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                url like '%videoshow%' 
                and event_name = 'page_view'
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
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) v on a.c_date = v.c_date 
  and a.email = v.email 
  left join (
    select 
      c_date, 
      email, 
      sum(pv) as commodity 
    from 
      (
        select 
          c_date, 
          email, 
          url, 
          sum(pv) as pv 
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
              gid, 
              url, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                (
                  page_template like '%commodity%' 
                  or url like '%commodity%'
                ) 
                and event_name = 'page_view'
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
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) cm on a.c_date = cm.c_date 
  and a.email = cm.email 
  left join (
    select 
      c_date, 
      email, 
      sum(pv) as slideshow 
    from 
      (
        select 
          c_date, 
          email, 
          url, 
          sum(pv) as pv 
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
              gid, 
              url, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                url like '%slideshow%' 
                and event_name = 'page_view'
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
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) d on a.c_date = d.c_date 
  and a.email = d.email 
  left join (
    select 
      c_date, 
      email, 
      sum(pv) as stock_report 
    from 
      (
        select 
          c_date, 
          email, 
          url, 
          sum(pv) as pv 
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
              gid, 
              url, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and et_product in ('Stock Report') 
              and event_name = 'page_view' 
              and page_template = 'Stock Report Viewer' 
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
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) e on a.c_date = e.c_date 
  and a.email = e.email

~~~

## Query-2: Objective: To find the Newsletter engagement  and section wise engagement of users who subscribed in jan 
~~~
Select 
  user_email, 
  Markets, 
  Tech, 
  Industry, 
  Others, 
  newsletter_subscribed, 
  mail_sent, 
  mail_open, 
  mail_click 
from 
  (
    select 
      user_email, 
      cast(
        DATETIME_ADD(
          DATETIME_ADD(
            cast(payment_success_date as datetime), 
            INTERVAL 5 HOUR
          ), 
          INTERVAL 30 Minute
        ) as date
      ) as payment_success_date, 
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
      ) as subscription_start_date, 
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
      ) as subscription_end_date 
    from 
      (
        SELECT 
          a.user_subscription_account_id, 
          max(payment_success_date) as payment_success_date, 
          max(
            plan_expiration_details_0_subscriptionStartDate
          ) as plan_expiration_details_0_subscriptionStartDate, 
          max(
            plan_expiration_details_0_subscriptionEndDateFromPayment
          ) as plan_expiration_details_0_subscriptionEndDateFromPayment 
        FROM 
          et - poc - 042021.all_user.billing_transactions a 
          left join all_user.user_subscriptions b on a.user_subscription_account_id = b.user_subscription_account_id 
        where 
          user_billing_transaction_plan_id not in (7, 6, 25) 
          and a.plan_name = 'Monthly Membership' 
          and user_billing_transaction_bill_transaction_source not in ('OFFLINE') 
          and bill_transaction_source != 'OFFLINE' 
          and bill_transaction_state = 'TRANSACTION_SUCCESS' 
          and a.plan_code not in ('author_plan', '1article') 
          and payment_success_date > '2021-12-31T18:30:00Z' 
          and payment_success_date < '2022-01-31T18:30:00Z' 
          and a.product_code in ('ETPR') 
        group by 
          1
      ) a 
      inner join et - poc - 042021.all_user.user_subscription_accounts b on a.user_subscription_account_id = SUBSTR(
        SUBSTR(b._id, 10), 
        1, 
        LENGTH(
          SUBSTR(b._id, 10)
        )-1
      ) 
    group by 
      1, 
      2, 
      3, 
      4
  ) a 
  left join (
    select 
      email, 
      round(
        sum(
          case when site_section = 'Markets' then perc else 0 end
        ), 
        1
      ) as Markets, 
      round(
        sum(
          case when site_section = 'Tech' then perc else 0 end
        ), 
        1
      ) as Tech, 
      round(
        sum(
          case when site_section = 'Industry' then perc else 0 end
        ), 
        1
      ) as Industry, 
      round(
        sum(
          case when site_section = 'other' then perc else 0 end
        ), 
        1
      ) as Others 
    from 
      (
        select 
          email, 
          site_section, 
          case when total != 0 then cast(articles as float64)/ cast(total as float64)* 100 else 0 end as perc 
        from 
          (
            select 
              email, 
              site_section, 
              articles, 
              sum(articles) over (partition by email) as total 
            from 
              (
                select 
                  email, 
                  site_section, 
                  count(*) as articles 
                from 
                  (
                    select 
                      email, 
                      site_section, 
                      msid 
                    from 
                      (
                        select 
                          cast(
                            SUBSTR(
                              CAST(__time AS string), 
                              1, 
                              10
                            ) as Date
                          ) as c_date, 
                          gid, 
                          case when site_section in ('Markets', 'Tech', 'Industry') then site_section else 'other' end as site_section, 
                          msid 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= '2021-12-31 18:30:00' 
                          and __time < '2022-03-31 18:30:00' 
                          and (
                            (
                              (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              ) 
                              and event_name = 'page_view' 
                              and et_product not in (
                                'Behind Sign In', 'Advertorial', 
                                'homepage'
                              )
                            ) 
                            or (
                              event_category in (
                                'Premium Articles PV Tracking', 
                                'Articleshow PV Tracking'
                              )
                            )
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
                          __time >= '2021-12-31 18:30:00' 
                          and __time < '2022-03-31 18:30:00' 
                          and gid is not null 
                          and email is not null 
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
          )
      ) 
    group by 
      1
  ) b on a.user_email = b.email 
  left join (
    select 
      email, 
      count(*) as newsletter_subscribed, 
      sum(mail_sent) as mail_sent, 
      sum(mail_open) as mail_open, 
      sum(mail_click) as mail_click 
    from 
      (
        select 
          email, 
          nl_id, 
          sum(
            case when event_name = 'mail_sent' then cnt else 0 end
          ) as mail_sent, 
          nl_id, 
          sum(
            case when event_name = 'mail_open' then cnt else 0 end
          ) as mail_open, 
          nl_id, 
          sum(
            case when event_name = 'mail_click' then cnt else 0 end
          ) as mail_click 
        from 
          (
            select 
              email, 
              nl_id, 
              event_name, 
              count(*) as cnt 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              client_source = 'et_newsletter' 
              and nl_name not like '%Test%' 
              and (
                mailer_type in ('NEWSLETTER') 
                or nl_name in ('News Digest')
              ) 
              and event_name in (
                'mail_sent', 'mail_open', 'mail_click'
              ) 
              and __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
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
  ) c on a.user_email = c.email

~~~
 
## Query-3: Objective: Day Wise Engagement of yearly plan paid users in March 2021
~~~
select 
  a.c_date, 
  user_email, 
  payment_success_date, 
  subscription_start_date, 
  subscription_end_date, 
  auto_renew, 
  plan_name, 
  Active_on_day, 
  Active_desktop, 
  Active_mweb, 
  Active_android, 
  Active_ios, 
  coalesce(Free_pv, 0) as free_pv, 
  coalesce(prime_pv, 0) as prime_pv, 
  coalesce(premium_pv, 0) as premium_pv, 
  coalesce(Free_pv, 0)+ coalesce(prime_pv, 0)+ coalesce(premium_pv, 0) as Total_articles_read_pv, 
  desktop_stories, 
  mweb_stories, 
  android_stories, 
  ios_stories, 
  Total_stories, 
  coalesce(Free_pv, 0)+ coalesce(prime_pv, 0)+ coalesce(premium_pv, 0)+ coalesce(company, 0)+ coalesce(video, 0)+ coalesce(commodity, 0)+ coalesce(slideshow, 0)+ coalesce(stock_report, 0) as Total_pv 
from 
  (
    select 
      user_email, 
      auto_renew, 
      plan_name, 
      cast(
        DATETIME_ADD(
          DATETIME_ADD(
            cast(payment_success_date as datetime), 
            INTERVAL 5 HOUR
          ), 
          INTERVAL 30 Minute
        ) as date
      ) as payment_success_date, 
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
      ) as subscription_start_date, 
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
      ) as subscription_end_date 
    from 
      (
        SELECT 
          a.user_subscription_account_id, 
          a.auto_renew, 
          a.plan_name, 
          max(payment_success_date) as payment_success_date, 
          max(
            plan_expiration_details_0_subscriptionStartDate
          ) as plan_expiration_details_0_subscriptionStartDate, 
          max(
            plan_expiration_details_0_subscriptionEndDateFromPayment
          ) as plan_expiration_details_0_subscriptionEndDateFromPayment 
        FROM 
          et - poc - 042021.all_user.billing_transactions a 
          left join all_user.user_subscriptions b on a.user_subscription_account_id = b.user_subscription_account_id 
        where 
          user_billing_transaction_plan_id not in (7, 6, 25) 
          and a.plan_name in (
            'Yearly Membership', 'Annual Membership', 
            'Annual Membership Without Trial Non Rec', 
            'Annual Membership Without Trial', 
            'INTRODUCTORY OFFER 12M', 'INTRODUCTORY OFFER 12ML', 
            ' Annual Student Plan'
          ) 
          and user_billing_transaction_bill_transaction_source not in ('OFFLINE') 
          and bill_transaction_source != 'OFFLINE' 
          and bill_transaction_state = 'TRANSACTION_SUCCESS' 
          and a.plan_code not in ('author_plan', '1article') 
          and payment_success_date > '2021-02-28T18:30:00Z' 
          and payment_success_date < '2021-03-31T18:30:00Z' 
          and a.product_code in ('ETPR') 
        group by 
          1, 
          2, 
          3
      ) a 
      inner join et - poc - 042021.all_user.user_subscription_accounts b on a.user_subscription_account_id = SUBSTR(
        SUBSTR(b._id, 10), 
        1, 
        LENGTH(
          SUBSTR(b._id, 10)
        )-1
      ) 
    group by 
      1, 
      2, 
      3, 
      4, 
      5, 
      6
  ) s 
  left join (
    select 
      email, 
      c_date, 
      Active_desktop, 
      Active_mweb, 
      Active_android, 
      Active_ios, 
      case when Active_desktop + Active_mweb + Active_android + Active_ios >= 1 then 1 else 0 end as Active_on_day 
    from 
      (
        select 
          email, 
          c_date, 
          sum(
            case when platform = 'desktop' then 1 else 0 end
          ) as Active_desktop, 
          sum(
            case when platform = 'mweb' then 1 else 0 end
          ) as Active_mweb, 
          sum(
            case when platform = 'android' then 1 else 0 end
          ) as Active_android, 
          sum(
            case when platform = 'ios' then 1 else 0 end
          ) as Active_ios 
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
                  ) then 'mweb' else lower(platform) end as platform, 
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
                  __time >= '2021-04-30 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
                  and client_source = 'growth_rx' 
                  and (
                    (
                      event_name = 'page_view' 
                      and (
                        (
                          url like '%companyid%' 
                          or (
                            url like '%/company/%' 
                            and url not like '%articleshow%' 
                            and platform in ('ios', 'android')
                          )
                        ) 
                        or (
                          (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and et_product not in (
                            'Behind Sign In', 'Advertorial', 
                            'homepage'
                          )
                        ) 
                        or (
                          (page_template like '%commodity%') 
                          or (url like '%commodity%')
                        ) 
                        or (url like '%slideshow%') 
                        or (
                          page_template = 'Stock Report Viewer' 
                          and et_product in ('Stock Report')
                        ) 
                        or (url like '%videoshow%')
                      )
                    ) 
                    or (
                      event_category in (
                        'Premium Articles PV Tracking', 
                        'Articleshow PV Tracking'
                      )
                    )
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
                  __time >= '2021-04-30 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
                  and gid not in (
                    '00000000-0000-0000-0000-000000000000'
                  ) 
                  and gid is not null 
                  and email is not null 
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
  ) a on s.user_email = a.email 
  left join (
    select 
      c_date, 
      email, 
      sum(
        case when et_product = 'Premium' then pv else 0 end
      ) as premium_pv, 
      sum(
        case when et_product = 'Prime Plus' then pv else 0 end
      ) as prime_pv, 
      sum(
        case when et_product = 'Free' then pv else 0 end
      ) as Free_pv 
    from 
      (
        select 
          c_date, 
          email, 
          et_product, 
          sum(pv) as pv 
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
              gid, 
              case when et_product = 'Premium' then 'Premium' when et_product = 'Prime Plus' then 'Prime Plus' else 'Free' end as et_product, 
              msid, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                (
                  (
                    url like '%articleshow%' 
                    or page_template like '%articleshow%'
                  ) 
                  and event_name = 'page_view' 
                  and et_product not in (
                    'Behind Sign In', 'Advertorial', 
                    'homepage'
                  )
                ) 
                or (
                  event_category in (
                    'Premium Articles PV Tracking', 
                    'Articleshow PV Tracking'
                  )
                )
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
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) b on a.email = b.email 
  and a.c_date = b.c_date 
  left join (
    select 
      c_date, 
      email, 
      desktop_stories, 
      mweb_stories, 
      android_stories, 
      ios_stories, 
      desktop_stories + mweb_stories + android_stories + ios_stories as Total_stories 
    from 
      (
        select 
          c_date, 
          email, 
          sum(
            case when platform = 'desktop' then article_read else 0 end
          ) as desktop_stories, 
          sum(
            case when platform = 'mweb' then article_read else 0 end
          ) as mweb_stories, 
          sum(
            case when platform = 'android' then article_read else 0 end
          ) as android_stories, 
          sum(
            case when platform = 'ios' then article_read else 0 end
          ) as ios_stories 
        from 
          (
            select 
              c_date, 
              email, 
              platform, 
              count(*) as article_read 
            from 
              (
                select 
                  c_date, 
                  email, 
                  platform, 
                  msid 
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
                      gid, 
                      case when (
                        (
                          platform = 'web' 
                          and device_category = 'mobile'
                        ) 
                        or platform in ('amp', 'pwa', 'PWA', 'AMP', 'mweb')
                      ) then 'mweb' else lower(platform) end as platform, 
                      msid 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= '2021-04-30 18:30:00' 
                      and __time < '2022-03-31 18:30:00' 
                      and (
                        (
                          (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and event_name = 'page_view' 
                          and et_product not in (
                            'Behind Sign In', 'Advertorial', 
                            'homepage'
                          )
                        ) 
                        or (
                          event_category in (
                            'Premium Articles PV Tracking', 
                            'Articleshow PV Tracking'
                          )
                        )
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
                      __time >= '2021-04-30 18:30:00' 
                      and __time < '2022-03-31 18:30:00' 
                      and gid not in (
                        '00000000-0000-0000-0000-000000000000'
                      ) 
                      and gid is not null 
                      and email is not null 
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
              2, 
              3
          ) 
        group by 
          1, 
          2
      )
  ) c on a.email = c.email 
  and a.c_date = c.c_date 
  left join (
    select 
      c_date, 
      email, 
      sum(pv) as company 
    from 
      (
        select 
          c_date, 
          email, 
          url, 
          sum(pv) as pv 
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
              gid, 
              url, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                (
                  url like '%companyid%' 
                  or (
                    url like '%/company/%' 
                    and url not like '%articleshow%' 
                    and platform in ('ios', 'android')
                  )
                ) 
                and event_name = 'page_view'
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
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) ac on a.c_date = ac.c_date 
  and a.email = ac.email 
  left join (
    select 
      c_date, 
      email, 
      sum(pv) as video 
    from 
      (
        select 
          c_date, 
          email, 
          url, 
          sum(pv) as pv 
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
              gid, 
              url, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                url like '%videoshow%' 
                and event_name = 'page_view'
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
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) v on a.c_date = v.c_date 
  and a.email = v.email 
  left join (
    select 
      c_date, 
      email, 
      sum(pv) as commodity 
    from 
      (
        select 
          c_date, 
          email, 
          url, 
          sum(pv) as pv 
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
              gid, 
              url, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                (
                  page_template like '%commodity%' 
                  or url like '%commodity%'
                ) 
                and event_name = 'page_view'
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
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) cm on a.c_date = cm.c_date 
  and a.email = cm.email 
  left join (
    select 
      c_date, 
      email, 
      sum(pv) as slideshow 
    from 
      (
        select 
          c_date, 
          email, 
          url, 
          sum(pv) as pv 
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
              gid, 
              url, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                url like '%slideshow%' 
                and event_name = 'page_view'
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
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) d on a.c_date = d.c_date 
  and a.email = d.email 
  left join (
    select 
      c_date, 
      email, 
      sum(pv) as stock_report 
    from 
      (
        select 
          c_date, 
          email, 
          url, 
          sum(pv) as pv 
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
              gid, 
              url, 
              count(*) as pv 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and et_product in ('Stock Report') 
              and event_name = 'page_view' 
              and page_template = 'Stock Report Viewer' 
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
              __time >= '2021-04-30 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and gid not in (
                '00000000-0000-0000-0000-000000000000'
              ) 
              and gid is not null 
              and email is not null 
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
  ) e on a.c_date = e.c_date 
  and a.email = e.email

~~~

## Query-4: Objective: Query is used to analyze what behavior of users help to increase propensity to convert on different metrics:
~~~
select 
  f.gid, 
  active_days, 
  Active_desktop, 
  Active_app, 
  Active_mweb, 
  active_day_bf_subscription, 
  case when active_day_bf_subscription is not null then 1 else 0 end as converted, 
  desktop_stories, 
  mweb_stories, 
  app_stories, 
  Total_stories, 
  Total_stories_bf_subsc, 
  desktop_paywall, 
  mweb_paywall, 
  app_paywall, 
  Total_paywall, 
  paywall_bf_subsc 
from 
  (
    select 
      a.gid, 
      active_days, 
      Active_desktop, 
      Active_app, 
      Active_mweb 
    from 
      (
        select 
          gid, 
          count(*) as active_days 
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
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and client_source = 'growth_rx' 
              and event_name = 'page_view' 
            group by 
              1, 
              2
          ) 
        group by 
          1
      ) a 
      left join (
        select 
          gid, 
          sum(
            case when platform = 'desktop' then 1 else 0 end
          ) as Active_desktop, 
          sum(
            case when platform = 'app' then 1 else 0 end
          ) as Active_app, 
          sum(
            case when platform = 'mweb' then 1 else 0 end
          ) as Active_mweb 
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
              ) then 'mweb' when lower(platform) in ('android', 'ios') then 'app' else lower(platform) end as platform, 
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
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and client_source = 'growth_rx' 
              and event_name = 'page_view' 
            group by 
              1, 
              2, 
              3
          ) 
        group by 
          gid
      ) b on a.gid = b.gid
  ) f 
  left join (
    select 
      gid, 
      sum(
        case when c_date is null then 0 else 1 end
      ) as active_day_bf_subscription 
    from 
      (
        select 
          s.gid, 
          c_date 
        from 
          (
            select 
              gid, 
              max(
                cast(
                  SUBSTR(
                    CAST(
                      datetime(__time, 'Asia/Kolkata') AS string
                    ), 
                    1, 
                    10
                  ) as Date
                )
              ) as subscription_start_date 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                (
                  event_category = 'Subscription Flow' 
                  and event_action in ('Flow Completed', 'Success')
                ) 
                or (
                  event_category like '%Checkout%' 
                  and event_action = 'PayGateway' 
                  and platform in (
                    'ios', 'IOS', 'android', 'ANDROID'
                  ) 
                  and event_label = 'Completed'
                )
              ) 
              and gid is not null 
            group by 
              1
          ) s 
          left join (
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
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and client_source = 'growth_rx' 
              and event_name = 'page_view' 
            group by 
              1, 
              2
          ) bs on c_date < subscription_start_date 
          and s.gid = bs.gid 
        group by 
          1, 
          2
      ) 
    group by 
      1
  ) bf on f.gid = bf.gid 
  left join (
    select 
      v.gid, 
      desktop_stories, 
      mweb_stories, 
      app_stories, 
      Total_stories, 
      coalesce(Total_stories_bf_subsc, 0) as Total_stories_bf_subsc 
    from 
      (
        select 
          gid, 
          sum(desktop_stories) as desktop_stories, 
          sum(mweb_stories) as mweb_stories, 
          sum(app_stories) as app_stories, 
          sum(Total_stories) as Total_stories 
        from 
          (
            select 
              c_date, 
              gid, 
              desktop_stories, 
              mweb_stories, 
              app_stories, 
              desktop_stories + mweb_stories + app_stories as Total_stories 
            from 
              (
                select 
                  c_date, 
                  gid, 
                  sum(
                    case when platform = 'desktop' then article_read else 0 end
                  ) as desktop_stories, 
                  sum(
                    case when platform = 'mweb' then article_read else 0 end
                  ) as mweb_stories, 
                  sum(
                    case when platform = 'app' then article_read else 0 end
                  ) as app_stories 
                from 
                  (
                    select 
                      c_date, 
                      gid, 
                      platform, 
                      count(*) as article_read 
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
                          gid, 
                          case when (
                            (
                              platform = 'web' 
                              and device_category = 'mobile'
                            ) 
                            or platform in ('amp', 'pwa', 'PWA', 'AMP', 'mweb')
                          ) then 'mweb' when lower(platform) in ('android', 'ios') then 'app' else lower(platform) end as platform, 
                          msid 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= '2021-12-31 18:30:00' 
                          and __time < '2022-03-31 18:30:00' 
                          and (
                            (
                              (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              ) 
                              and event_name = 'page_view' 
                              and et_product not in (
                                'Behind Sign In', 'Advertorial', 
                                'homepage'
                              )
                            ) 
                            or (
                              event_category in (
                                'Premium Articles PV Tracking', 
                                'Articleshow PV Tracking'
                              )
                            )
                          ) 
                        group by 
                          1, 
                          2, 
                          3, 
                          4
                      ) a 
                    group by 
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
      ) v 
      left join (
        select 
          vs.gid, 
          sum(Total_stories) as Total_stories_bf_subsc 
        from 
          (
            select 
              c_date, 
              gid, 
              desktop_stories, 
              mweb_stories, 
              android_stories, 
              ios_stories, 
              desktop_stories + mweb_stories + android_stories + ios_stories as Total_stories 
            from 
              (
                select 
                  c_date, 
                  gid, 
                  sum(
                    case when platform = 'desktop' then article_read else 0 end
                  ) as desktop_stories, 
                  sum(
                    case when platform = 'mweb' then article_read else 0 end
                  ) as mweb_stories, 
                  sum(
                    case when platform = 'android' then article_read else 0 end
                  ) as android_stories, 
                  sum(
                    case when platform = 'ios' then article_read else 0 end
                  ) as ios_stories 
                from 
                  (
                    select 
                      c_date, 
                      gid, 
                      platform, 
                      count(*) as article_read 
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
                          gid, 
                          case when (
                            (
                              platform = 'web' 
                              and device_category = 'mobile'
                            ) 
                            or platform in ('amp', 'pwa', 'PWA', 'AMP', 'mweb')
                          ) then 'mweb' else lower(platform) end as platform, 
                          msid 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= '2021-12-31 18:30:00' 
                          and __time < '2022-03-31 18:30:00' 
                          and (
                            (
                              (
                                url like '%articleshow%' 
                                or page_template like '%articleshow%'
                              ) 
                              and event_name = 'page_view' 
                              and et_product not in (
                                'Behind Sign In', 'Advertorial', 
                                'homepage'
                              )
                            ) 
                            or (
                              event_category in (
                                'Premium Articles PV Tracking', 
                                'Articleshow PV Tracking'
                              )
                            )
                          ) 
                        group by 
                          1, 
                          2, 
                          3, 
                          4
                      ) a 
                    group by 
                      1, 
                      2, 
                      3
                  ) 
                group by 
                  1, 
                  2
              )
          ) v 
          right join (
            select 
              gid, 
              max(
                cast(
                  SUBSTR(
                    CAST(
                      datetime(__time, 'Asia/Kolkata') AS string
                    ), 
                    1, 
                    10
                  ) as Date
                )
              ) as subscription_start_date 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                (
                  event_category = 'Subscription Flow' 
                  and event_action in ('Flow Completed', 'Success')
                ) 
                or (
                  event_category like '%Checkout%' 
                  and event_action = 'PayGateway' 
                  and platform in (
                    'ios', 'IOS', 'android', 'ANDROID'
                  ) 
                  and event_label = 'Completed'
                )
              ) 
              and gid is not null 
            group by 
              1
          ) vs on v.gid = vs.gid 
          and c_date < subscription_start_date 
        group by 
          1
      ) vs on v.gid = vs.gid
  ) v on f.gid = v.gid 
  left join (
    select 
      p.gid, 
      desktop_paywall, 
      mweb_paywall, 
      app_paywall, 
      desktop_paywall + mweb_paywall + app_paywall as Total_paywall, 
      coalesce(paywall_bf_subsc, 0) as paywall_bf_subsc 
    from 
      (
        select 
          gid, 
          sum(desktop_paywall) as desktop_paywall, 
          sum(mweb_paywall) as mweb_paywall, 
          sum(app_paywall) as app_paywall 
        from 
          (
            select 
              c_date, 
              gid, 
              sum(
                case when platform = 'desktop' then paywall else 0 end
              ) as desktop_paywall, 
              sum(
                case when platform = 'mweb' then paywall else 0 end
              ) as mweb_paywall, 
              sum(
                case when platform = 'app' then paywall else 0 end
              ) as app_paywall 
            from 
              (
                select 
                  gid, 
                  platform, 
                  c_date, 
                  count(*) as paywall 
                from 
                  (
                    select 
                      gid, 
                      msid, 
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
                        (
                          platform = 'web' 
                          and device_category = 'mobile'
                        ) 
                        or platform in ('amp', 'pwa', 'PWA', 'AMP', 'mweb')
                      ) then 'mweb' when lower(platform) in ('android', 'ios') then 'app' else lower(platform) end as platform 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      client_source = 'growth_rx' 
                      and __time >= '2021-12-31 18:30:00' 
                      and __time < '2022-03-31 18:30:00' 
                      and (
                        (
                          event_name = 'page_view' 
                          and (
                            lower(et_product) like 'prime%' 
                            or lower(et_product) = 'Premium'
                          ) 
                          and (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          )
                        ) 
                        or (url like '%primearticleshow%') 
                        or (
                          event_category in ('Premium Articles PV Tracking') 
                          and device_category in ('desktop', 'mobile')
                        ) 
                        or (
                          event_category = 'Articleshow PV Tracking' 
                          and device_category in ('desktop', 'mobile') 
                          and et_product = 'Adaptive Paid'
                        ) 
                        or (
                          paywall_experiment is not null 
                          and event_name = 'page_view' 
                          and url like '%articleshow%' 
                          and msid is not null
                        )
                      ) 
                      and (
                        (
                          lower(current_subscriber_status) not like '%paid%' 
                          and lower(current_subscriber_status) not like '%active%'
                        ) 
                        or (
                          current_subscriber_status is null
                        )
                      ) 
                      and (
                        (
                          lower(user_subscription_status) not like '%active%' 
                          and lower(user_subscription_status) not like '%paid%'
                        ) 
                        or user_subscription_status is null
                      ) 
                      and msid is not null 
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
          1
      ) p 
      left join (
        select 
          a.gid, 
          coalesce(
            sum(paywall), 
            0
          ) as paywall_bf_subsc 
        from 
          (
            select 
              gid, 
              max(
                cast(
                  SUBSTR(
                    CAST(
                      datetime(__time, 'Asia/Kolkata') AS string
                    ), 
                    1, 
                    10
                  ) as Date
                )
              ) as subscription_start_date 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-12-31 18:30:00' 
              and __time < '2022-03-31 18:30:00' 
              and (
                (
                  event_category = 'Subscription Flow' 
                  and event_action in ('Flow Completed', 'Success')
                ) 
                or (
                  event_category like '%Checkout%' 
                  and event_action = 'PayGateway' 
                  and platform in (
                    'ios', 'IOS', 'android', 'ANDROID'
                  ) 
                  and event_label = 'Completed'
                )
              ) 
              and gid is not null 
            group by 
              1
          ) a 
          left join (
            select 
              gid, 
              c_date, 
              count(*) as paywall 
            from 
              (
                select 
                  gid, 
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
                  client_source = 'growth_rx' 
                  and __time >= '2021-12-31 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
                  and (
                    (
                      event_name = 'page_view' 
                      and (
                        lower(et_product) like 'prime%' 
                        or lower(et_product) = 'Premium'
                      ) 
                      and (
                        url like '%articleshow%' 
                        or page_template like '%articleshow%'
                      )
                    ) 
                    or (url like '%primearticleshow%') 
                    or (
                      event_category in ('Premium Articles PV Tracking') 
                      and device_category in ('desktop', 'mobile')
                    ) 
                    or (
                      event_category = 'Articleshow PV Tracking' 
                      and device_category in ('desktop', 'mobile') 
                      and et_product = 'Adaptive Paid'
                    ) 
                    or (
                      paywall_experiment is not null 
                      and event_name = 'page_view' 
                      and url like '%articleshow%' 
                      and msid is not null
                    )
                  ) 
                  and (
                    (
                      lower(current_subscriber_status) not like '%paid%' 
                      and lower(current_subscriber_status) not like '%active%'
                    ) 
                    or (
                      current_subscriber_status is null
                    )
                  ) 
                  and (
                    (
                      lower(user_subscription_status) not like '%active%' 
                      and lower(user_subscription_status) not like '%paid%'
                    ) 
                    or user_subscription_status is null
                  ) 
                  and msid is not null 
                group by 
                  1, 
                  2, 
                  3
              ) 
            group by 
              1, 
              2
          ) b on a.gid = b.gid 
          and c_date < subscription_start_date 
        group by 
          1
      ) ps on p.gid = ps.gid
  ) p on p.gid = f.gid 
where 
  f.gid not in (
    '00000000-0000-0000-0000-000000000000’)

~~~
## Query-5: Objective: To find the daywise users on mweb unique based on there email and gid
~~~
select 
  c_date, 
  count(*) as users 
from 
  (
    select 
      case when email is null then a.gid else email end as user, 
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
          __time >= '2022-03-31 18:30:00' 
          and __time < '2022-04-12 18:30:00' 
          and (
            lower(platform) in ('mweb', 'pwa')
          ) 
        group by 
          1, 
          2
      ) a 
      left join (
        select 
          gid, 
          email 
        from 
          `et-poc-042021.all_user.EtClientEvents` 
        where 
          __time >= '2022-03-01 18:30:00' 
          and __time < '2022-04-12 18:30:00' 
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
      1, 
      2
  ) 
group by 
  1 
order by 
  c_date desc

~~~

## Query-6: Objective: Queries used to find  list of users with email and phone number who left ET being free loyal users from Dec till march
~~~
select 
  a.user, 
  phone 
from 
  (
    select 
      user 
    from 
      (
        select 
          a.user 
        from 
          (
            select 
              user 
            from 
              (
                select 
                  user, 
                  yr, 
                  mon, 
                  sum(status) as status, 
                  count(*) as sessions 
                from 
                  (
                    select 
                      email as user, 
                      extract(
                        year 
                        from 
                          c_date
                      ) as yr, 
                      extract(
                        Month 
                        from 
                          c_date
                      ) as mon, 
                      sessionId, 
                      sum(status) as status 
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
                          sum(
                            case when (
                              (
                                (
                                  lower(user_subscription_status) like '%paid%'
                                ) 
                                or (
                                  lower(current_subscriber_status) like '%paid%'
                                ) 
                                and (
                                  lower(current_subscriber_status) like '%active%'
                                ) 
                                or (
                                  lower(user_subscription_status) like '%active%'
                                )
                              )
                            ) then 1 else 0 end
                          ) as status, 
                          sessionId 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= '2021-11-30 18:30:00' 
                          and __time < '2021-12-31 18:30:00' 
                        group by 
                          1, 
                          2, 
                          4
                      ) a 
                      inner join (
                        select 
                          gid, 
                          email 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= '2021-11-30 18:30:00' 
                          and __time < '2022-03-31 18:30:00' 
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
            where 
              status = 0 
              and sessions > 21 
            group by 
              1
          ) a 
          left join (
            select 
              email as user 
            from 
              (
                select 
                  gid 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2021-12-31 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
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
                  __time >= '2021-11-30 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
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
          ) b on a.user = b.user 
        where 
          b.user is null 
        group by 
          1 
        union 
          distinct 
        select 
          a.user 
        from 
          (
            select 
              user 
            from 
              (
                select 
                  user, 
                  yr, 
                  mon, 
                  sum(status) as status, 
                  count(*) as sessions 
                from 
                  (
                    select 
                      email as user, 
                      extract(
                        year 
                        from 
                          c_date
                      ) as yr, 
                      extract(
                        Month 
                        from 
                          c_date
                      ) as mon, 
                      sessionId, 
                      sum(status) as status 
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
                          sum(
                            case when (
                              (
                                (
                                  lower(user_subscription_status) like '%paid%'
                                ) 
                                or (
                                  lower(current_subscriber_status) like '%paid%'
                                ) 
                                and (
                                  lower(current_subscriber_status) like '%active%'
                                ) 
                                or (
                                  lower(user_subscription_status) like '%active%'
                                )
                              )
                            ) then 1 else 0 end
                          ) as status, 
                          sessionId 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= '2021-12-31 18:30:00' 
                          and __time < '2022-01-31 18:30:00' 
                        group by 
                          1, 
                          2, 
                          4
                      ) a 
                      inner join (
                        select 
                          gid, 
                          email 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= '2021-11-30 18:30:00' 
                          and __time < '2022-03-31 18:30:00' 
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
            where 
              status = 0 
              and sessions > 21 
            group by 
              1
          ) a 
          left join (
            select 
              email as user 
            from 
              (
                select 
                  gid 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2022-01-31 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
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
                  __time >= '2021-11-30 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
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
          ) b on a.user = b.user 
        where 
          b.user is null 
        group by 
          1 
        union 
          distinct 
        select 
          a.user 
        from 
          (
            select 
              user 
            from 
              (
                select 
                  user, 
                  yr, 
                  mon, 
                  sum(status) as status, 
                  count(*) as sessions 
                from 
                  (
                    select 
                      email as user, 
                      extract(
                        year 
                        from 
                          c_date
                      ) as yr, 
                      extract(
                        Month 
                        from 
                          c_date
                      ) as mon, 
                      sessionId, 
                      sum(status) as status 
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
                          sum(
                            case when (
                              (
                                (
                                  lower(user_subscription_status) like '%paid%'
                                ) 
                                or (
                                  lower(current_subscriber_status) like '%paid%'
                                ) 
                                and (
                                  lower(current_subscriber_status) like '%active%'
                                ) 
                                or (
                                  lower(user_subscription_status) like '%active%'
                                )
                              )
                            ) then 1 else 0 end
                          ) as status, 
                          sessionId 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= '2022-01-31 18:30:00' 
                          and __time < '2022-02-28 18:30:00' 
                        group by 
                          1, 
                          2, 
                          4
                      ) a 
                      inner join (
                        select 
                          gid, 
                          email 
                        from 
                          `et-poc-042021.all_user.EtClientEvents` 
                        where 
                          __time >= '2021-11-30 18:30:00' 
                          and __time < '2022-03-31 18:30:00' 
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
            where 
              status = 0 
              and sessions > 21 
            group by 
              1
          ) a 
          left join (
            select 
              email as user 
            from 
              (
                select 
                  gid 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2022-02-28 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
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
                  __time >= '2021-11-30 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
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
          ) b on a.user = b.user 
        where 
          b.user is null 
        group by 
          1
      ) 
    group by 
      1
  ) a 
  left join (
    select 
      email as user, 
      phone 
    from 
      (
        select 
          gid, 
          phone 
        from 
          `et-poc-042021.all_user.EtClientEvents` 
        where 
          __time >= '2021-11-30 18:30:00' 
          and __time < '2022-03-31 18:30:00' 
          and phone is not null 
          and phone != '-' 
        group by 
          1, 
          2
      ) a 
      left join (
        select 
          gid, 
          email 
        from 
          `et-poc-042021.all_user.EtClientEvents` 
        where 
          __time >= '2021-11-30 18:30:00' 
          and __time < '2022-03-31 18:30:00' 
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
      1, 
      2
  ) b on a.user = b.user 
group by 
  1, 
  2

~~~
## Query-7: Objective: Queries used to find list of free loyal retained users from Dec 21 to Mar 22
~~~
select 
  a.user, 
  phone 
from 
  (
    select 
      user 
    from 
      (
        select 
          user, 
          count(*) as loyal_in_months 
        from 
          (
            select 
              user, 
              yr, 
              mon, 
              sum(status) as status, 
              count(*) as sessions 
            from 
              (
                select 
                  case when email is null then a.gid else email end as user, 
                  extract(
                    year 
                    from 
                      c_date
                  ) as yr, 
                  extract(
                    Month 
                    from 
                      c_date
                  ) as mon, 
                  sessionId, 
                  sum(status) as status 
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
                      sum(
                        case when (
                          (
                            (
                              lower(user_subscription_status) like '%paid%'
                            ) 
                            or (
                              lower(current_subscriber_status) like '%paid%'
                            ) 
                            and (
                              lower(current_subscriber_status) like '%active%'
                            ) 
                            or (
                              lower(user_subscription_status) like '%active%'
                            )
                          )
                        ) then 1 else 0 end
                      ) as status, 
                      sessionId 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= '2021-11-30 18:30:00' 
                      and __time < '2022-03-31 18:30:00' 
                    group by 
                      1, 
                      2, 
                      4
                  ) a 
                  left join (
                    select 
                      gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= '2021-11-30 18:30:00' 
                      and __time < '2022-03-31 18:30:00' 
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
        where 
          sessions > 21 
          and status = 0 
        group by 
          1 
        having 
          loyal_in_months = 4
      ) 
    group by 
      1
  ) a 
  left join (
    select 
      case when email is null then a.gid else email end as user, 
      phone 
    from 
      (
        select 
          gid, 
          phone 
        from 
          `et-poc-042021.all_user.EtClientEvents` 
        where 
          __time >= '2021-11-30 18:30:00' 
          and __time < '2022-03-31 18:30:00' 
          and phone is not null 
        group by 
          1, 
          2
      ) a 
      left join (
        select 
          gid, 
          email 
        from 
          `et-poc-042021.all_user.EtClientEvents` 
        where 
          __time >= '2021-11-30 18:30:00' 
          and __time < '2022-03-31 18:30:00' 
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
      1, 
      2
  ) b on a.user = b.user 
group by 
  1, 
  2

~~~
## Query-8: Objective: Queries used to Analyze the movement of users from one user engagement bucket to another in different months

~~~
select 
  * 
from 
  (
    select 
      yr, 
      mon, 
      user, 
      case when status >= 1 then 'paid' when sessions = 1 then 'window shopper' when sessions between 2 
      and 7 then 'repeat' when sessions between 8 
      and 21 then 'engaged' when sessions > 21 then 'loyal' else 'other' end as user_buck 
    from 
      (
        select 
          user, 
          yr, 
          mon, 
          sum(status) as status, 
          count(*) as sessions 
        from 
          (
            select 
              case when email is null then a.gid else email end as user, 
              extract(
                year 
                from 
                  c_date
              ) as yr, 
              extract(
                Month 
                from 
                  c_date
              ) as mon, 
              sessionId, 
              sum(status) as status 
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
                  sum(
                    case when (
                      (
                        (
                          lower(user_subscription_status) like '%paid%'
                        ) 
                        or (
                          lower(current_subscriber_status) like '%paid%'
                        ) 
                        and (
                          lower(current_subscriber_status) like '%active%'
                        ) 
                        or (
                          lower(user_subscription_status) like '%active%'
                        )
                      )
                    ) then 1 else 0 end
                  ) as status, 
                  sessionId 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2022-02-28 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
                group by 
                  1, 
                  2, 
                  4
              ) a 
              left join (
                select 
                  gid, 
                  email 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2021-11-30 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
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
  ) a full 
  outer join (
    select 
      yr, 
      mon, 
      user, 
      case when status >= 1 then 'paid' when sessions = 1 then 'window shopper' when sessions between 2 
      and 7 then 'repeat' when sessions between 8 
      and 21 then 'engaged' when sessions > 21 then 'loyal' else 'other' end as user_buck 
    from 
      (
        select 
          user, 
          yr, 
          mon, 
          sum(status) as status, 
          count(*) as sessions 
        from 
          (
            select 
              case when email is null then a.gid else email end as user, 
              extract(
                year 
                from 
                  c_date
              ) as yr, 
              extract(
                Month 
                from 
                  c_date
              ) as mon, 
              sessionId, 
              sum(status) as status 
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
                  sum(
                    case when (
                      (
                        (
                          lower(user_subscription_status) like '%paid%'
                        ) 
                        or (
                          lower(current_subscriber_status) like '%paid%'
                        ) 
                        and (
                          lower(current_subscriber_status) like '%active%'
                        ) 
                        or (
                          lower(user_subscription_status) like '%active%'
                        )
                      )
                    ) then 1 else 0 end
                  ) as status, 
                  sessionId 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2022-01-31 18:30:00' 
                  and __time < '2022-02-28 18:30:00' 
                group by 
                  1, 
                  2, 
                  4
              ) a 
              left join (
                select 
                  gid, 
                  email 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2021-11-30 18:30:00' 
                  and __time < '2022-03-31 18:30:00' 
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
  ) b on a.user = b.user

~~~
## Query-9: Objective: Query used to generate the links which is used on different pages on  desktop which in return increase the visibility of pages on web browsers

## Interlinking query
~~~ 
select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 't2-5archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= cast(current_date-5 as timestamp) and __time >= cast(current_date-2 as timestamp)
group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 't2-5archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= cast(current_date-5 as timestamp) and __time >= cast(current_date-2 as timestamp)
group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 't2-5archivedarticles' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= cast(current_date-5 as timestamp) and __time >= cast(current_date-2 as timestamp)
group by msid)
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
 
 
union all

select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 't5-30archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day and __time<=CURRENT_TIMESTAMP-interval '5' Day
group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 't5-30archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day and __time<=CURRENT_TIMESTAMP-interval '5' Day
group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 't5-30archivedarticles' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day and __time<=CURRENT_TIMESTAMP-interval '5' Day
group by msid)
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
union all
 
select * from (select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 't-30archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid not in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day
group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 't-30archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid not in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 't-30archivedarticles' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid not in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day
group by msid)
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text)
where trim(link_text) not like ''
 
union all
 
select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'primearticle' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template  in ('primearticleshow','amp_primepremiumarticleshow')
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'primearticle' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template  in ('primearticleshow','amp_primepremiumarticleshow')
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'primearticle' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template  in ('primearticleshow','amp_primepremiumarticleshow')
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 

union all

--Commodity

select a.template,a.link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select ‘commodity' as template,link,case when link_text like '%,language%' then split(split(link_text,'symbol-')[SAFE_OFFSET(1)],',language')[SAFE_OFFSET(0)]
else split(split(link_text,'symbol-')[SAFE_OFFSET(1)],'.cms')[SAFE_OFFSET(0)] end as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%commoditysummary%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select ‘commodity' as template,link,case when link_text like '%,language%' then split(split(link_text,'symbol-')[SAFE_OFFSET(1)],',language')[SAFE_OFFSET(0)]
else split(split(link_text,'symbol-')[SAFE_OFFSET(1)],'.cms')[SAFE_OFFSET(0)] end as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%commoditysummary%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select ‘commodity' as template,link,case when link_text like '%,language%' then split(split(link_text,'symbol-')[SAFE_OFFSET(1)],',language')[SAFE_OFFSET(0)]
else split(split(link_text,'symbol-')[SAFE_OFFSET(1)],'.cms')[SAFE_OFFSET(0)] end as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%commoditysummary%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like '' and a.link_text not like '%2%’
 
Union all
 
--Company default
 
select a.template,a.link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'companydefault' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(1)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%company_default%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'companydefault' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(1)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%company_default%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'companydefault' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(1)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%company_default%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
 
union all
 
--company id
 
select template,case when companyshortname is null or companyshortname in ('\\N') then Concat(INITCAP(company_name),' Share Price') else concat(companyshortname,' Share Price') end as link_text ,link,importance from
(select a.template,trim(REGEXP_REPLACE(REGEXP_REPLACE(a.link_text,'companyid-',' '),'.cms','')) as company_id,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance,company_name from 
(select a.template,a.link_text,a.importance,link,company_name from
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'company' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%stocks/companyid%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'company' as template,link,link_text,REGEXP_REPLACE(company_name,'-',' ') as company_name
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as company_name from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%stocks/companyid%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3,4)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'company' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%stocks/companyid%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text  not like '')a
left join
`et-poc-042021.interlink.seo_company_name`b on cast(a.company_id as int64)=cast(CompanyID as int64) and SAFE_CAST(a.company_id AS FLOAT64) is not NULL
where SAFE_CAST(a.company_id AS FLOAT64) is not NULL
 
 
 
union all
 
--definition
 
select a.template,a.link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select ‘definition' as template,link,regexp_replace(regexp_replace(link_text,'-',' '),'%20',' ') as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%definition%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select ‘definition' as template,link,regexp_replace(regexp_replace(link_text,'-',' '),'%20',' ') as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%definition%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select ‘definition' as template,link, regexp_replace(regexp_replace(link_text,'-',' '),'%20',' ') as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%definition%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
 
 
Union all
 
--IFSC
 
select a.template,REGEXP_REPLACE(REGEXP_REPLACE(concat(concat(concat(concat(coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(0)],5),'-',' '),''),coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(3)],7),'-',' '),'')),coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(2)],9),'-',' '),'')),coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(1)],6),'-',' '),'')),coalesce(upper(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(4)],9),'-',' ')),'')),'.cms',''),'.cms','') as link_text
--coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(0)],5),'-',' '),'') as bank,
--coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(1)],6),'-',' '),'') as state,
--coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(2)],9),'-',' '),'')  as district,
--coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(3)],7),'-',' '),'')  as branch,
--coalesce(upper(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(4)],9),'.cms',' ')),'') as ifsc
,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'IFSC' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%ifsccode%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'IFSC' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%ifsccode%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'IFSC' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%ifsccode%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
union all
 
 
--Slideshow
 
select a.template,REGEXP_REPLACE(a.link_text,'-',' ') as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select ‘slideshow' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%slideshow%'
and url not like '%webstories%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select ‘slideshow' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%slideshow%'
and url not like '%webstories%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select ‘slideshow' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%slideshow%'
and url not like '%webstories%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
Union all
 
--Storylisting
 
select a.template,REGEXP_REPLACE(REGEXP_REPLACE(a.link_text,'-',' '),'.cms','') as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select ‘storylisting' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%storylisting%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select ‘storylisting' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%storylisting%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select ‘storylisting' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%storylisting%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
and SAFE_CAST(a.link_text AS FLOAT64) is NULL
 
 
union all
 
—-videoshow
 
select a.template,REGEXP_REPLACE(a.link_text,'-',' ') as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select ‘videsoshow' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%videoshow%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select ‘videsoshow' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%videoshow%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select ‘videsoshow' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%videoshow%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
 
union all
 
—-topic
 
select template,link_text,link,importance from (select template,initcap(link_text) as link_text,link,importance,sum(case when lower(link_text) like lower(string_field_0) then 1 else 0 end) as toxic from
(select a.template,REGEXP_REPLACE(REGEXP_REPLACE(a.link_text,'-',' '),'.cms','') as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'topic' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%/topic/%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'topic' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%/topic/%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'topic' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%/topic/%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
and SAFE_CAST(a.link_text AS FLOAT64) is NULL
and a.link_text not in ('videos','news','photos'))a
cross join `et-poc-042021.interlink.Toxic_keywords`b
group by template,link_text,link,importance
having toxic =0)
 
 
Union all
 
—-mffactsheet
 
select template,case when NameOfScheme is null then initcap(scheme_name) else NameOfScheme end  as link_text,link,importance from (select a.template,trim(REGEXP_REPLACE(REGEXP_REPLACE(a.link_text,'schemeid-',' '),'.cms','')) as scheme_id,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance,scheme_name from  (select a.template,a.link_text,a.importance,link,scheme_name from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'mffactsheet' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%mffactsheet%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'mffactsheet' as template,link,link_text,REGEXP_REPLACE(scheme_name,'-',' ')as scheme_name
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as scheme_name from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%mffactsheet%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3,4)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'mffactsheet' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%mffactsheet%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like '')a
left join
`et-poc-042021.interlink.mf_schemes` b on cast(a.scheme_id as int64)=b.SchemeId and SAFE_CAST(a.scheme_id AS FLOAT64) is not NULL
where SAFE_CAST(a.scheme_id AS FLOAT64) is not NULL
 
 ~~~
 
## Final Query for interlinking data
 
~~~ 
select trim(template) as template ,link,trim(link_text) as link_text,importance,concat(template,row_no) as link_id from
(select ROW_NUMBER() OVER(PARTITION BY template order by importance desc ) as row_no,template,link_text,link,importance from
(
select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 't2-5archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= cast(current_date-5 as timestamp) and __time >= cast(current_date-2 as timestamp)
group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 't2-5archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= cast(current_date-5 as timestamp) and __time >= cast(current_date-2 as timestamp)
group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 't2-5archivedarticles' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= cast(current_date-5 as timestamp) and __time >= cast(current_date-2 as timestamp)
group by msid)
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
 
 
union all
 
select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 't5-30archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day and __time<=CURRENT_TIMESTAMP-interval '5' Day
group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 't5-30archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day and __time<=CURRENT_TIMESTAMP-interval '5' Day
group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 't5-30archivedarticles' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day and __time<=CURRENT_TIMESTAMP-interval '5' Day
group by msid)
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
union all
 
select * from (select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 't-30archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid not in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day
group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 't-30archivedarticles' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid not in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day group by msid)
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 't-30archivedarticles' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%articleshow%'
and page_template not in ('primearticleshow','amp_primepremiumarticleshow')
and msid not in (select msid from `et-poc-042021.all_user.EtClientEvents`
where event_name='entity_added'
and __time >= CURRENT_TIMESTAMP-interval '30' Day
group by msid)
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text)
where trim(link_text) not like ''
 
union all
 
select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'primearticle' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template  in ('primearticleshow','amp_primepremiumarticleshow')
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'primearticle' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template  in ('primearticleshow','amp_primepremiumarticleshow')
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'primearticle' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-5 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template  in ('primearticleshow','amp_primepremiumarticleshow')
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
 
union all
 
--Commodity
 
select a.template,initcap(a.link_text) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'commodity' as template,link,case when link_text like '%,language%' then split(split(link_text,'symbol-')[SAFE_OFFSET(1)],',language')[SAFE_OFFSET(0)]
else split(split(link_text,'symbol-')[SAFE_OFFSET(1)],'.cms')[SAFE_OFFSET(0)] end as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%commoditysummary%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'commodity' as template,link,case when link_text like '%,language%' then split(split(link_text,'symbol-')[SAFE_OFFSET(1)],',language')[SAFE_OFFSET(0)]
else split(split(link_text,'symbol-')[SAFE_OFFSET(1)],'.cms')[SAFE_OFFSET(0)] end as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%commoditysummary%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'commodity' as template,link,case when link_text like '%,language%' then split(split(link_text,'symbol-')[SAFE_OFFSET(1)],',language')[SAFE_OFFSET(0)]
else split(split(link_text,'symbol-')[SAFE_OFFSET(1)],'.cms')[SAFE_OFFSET(0)] end as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%commoditysummary%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like '' and a.link_text not like '%2%'
 
Union all
 
--Company default
 
select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text ,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'companydefault' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(1)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%company_default%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'companydefault' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(1)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%company_default%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'companydefault' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(1)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%company_default%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
 
union all
 
--company id
 
select template,case when companyshortname is null or companyshortname in ('\\N') then Concat(INITCAP(company_name),' Share Price') else concat(companyshortname,' Share Price') end as link_text ,link,importance from
(select a.template,trim(REGEXP_REPLACE(REGEXP_REPLACE(a.link_text,'companyid-',' '),'.cms','')) as company_id,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance,company_name from 
(select a.template,a.link_text,a.importance,link,company_name from
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'company' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%stocks/companyid%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'company' as template,link,link_text,REGEXP_REPLACE(company_name,'-',' ') as company_name
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as company_name from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%stocks/companyid%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
and url not like '%/hindi/%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3,4)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'company' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%stocks/companyid%'
and url not like '%/hindi/%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text  not like '')a
left join
`et-poc-042021.interlink.seo_company_name`b on cast(a.company_id as int64)=cast(CompanyID as int64) and SAFE_CAST(a.company_id AS FLOAT64) is not NULL
where SAFE_CAST(a.company_id AS FLOAT64) is not NULL
 
 
 
union all
 
--definition
 
select a.template,initcap(a.link_text) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'definition' as template,link,regexp_replace(regexp_replace(link_text,'-',' '),'%20',' ') as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%definition%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'definition' as template,link,regexp_replace(regexp_replace(link_text,'-',' '),'%20',' ') as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%definition%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'definition' as template,link, regexp_replace(regexp_replace(link_text,'-',' '),'%20',' ') as link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%definition%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
and length(a.link_text)!=1
 
 
Union all
 
--IFSC
 
select a.template,REGEXP_REPLACE(REGEXP_REPLACE(concat(concat(concat(concat(initcap(coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(0)],5),'-',' '),'')),initcap(coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(3)],7),'-',' '),''))),initcap(coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(2)],9),'-',' '),''))),initcap(coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(1)],6),'-',' '),''))),coalesce(upper(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(4)],9),'-',' ')),'')),'.Cms',''),'.CMS','')  as link_text
--coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(0)],5),'-',' '),'') as bank,
--coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(1)],6),'-',' '),'') as state,
--coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(2)],9),'-',' '),'')  as district,
--coalesce(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(3)],7),'-',' '),'')  as branch,
--coalesce(upper(REGEXP_REPLACE(substring(split(a.link_text)[SAFE_OFFSET(4)],9),'.cms',' ')),'') as ifsc
,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'IFSC' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%ifsccode%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'IFSC' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%ifsccode%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'IFSC' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%ifsccode%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
union all
 
 
--Slideshow
 
select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'slideshow' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%slideshow%'
and url not like '%webstories%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'slideshow' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%slideshow%'
and url not like '%webstories%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'slideshow' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%slideshow%'
and url not like '%webstories%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
Union all
 
--Storylisting
 
select a.template,initcap(REGEXP_REPLACE(REGEXP_REPLACE(a.link_text,'-',' '),'.cms','')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'storylisting' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%storylisting%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'storylisting' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%storylisting%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'storylisting' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%storylisting%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
and SAFE_CAST(a.link_text AS FLOAT64) is NULL
 
 
union all
 
--videoshow
 
select a.template,initcap(REGEXP_REPLACE(a.link_text,'-',' ')) as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'videoshow' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%videoshow%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'videsoshow' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%videoshow%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'videsoshow' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%videoshow%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
 
 
union all
 
--topic
 
select template,link_text,link,importance from (select template,initcap(link_text) as link_text,link,importance,sum(case when lower(link_text) like lower(string_field_0) then 1 else 0 end) as toxic from
(select a.template,REGEXP_REPLACE(REGEXP_REPLACE(a.link_text,'-',' '),'.cms','') as link_text,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance from  (select a.template,a.link_text,a.importance,link from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'topic' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%/topic/%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'topic' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%/topic/%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'topic' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and url like '%/topic/%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like ''
and SAFE_CAST(a.link_text AS FLOAT64) is NULL
and a.link_text not in ('videos','news','photos'))a
cross join `et-poc-042021.interlink.Toxic_keywords`b
group by template,link_text,link,importance
having toxic =0)
 
 
Union all
 
--mffactsheet
 
select template,case when NameOfScheme is null then initcap(scheme_name) else NameOfScheme end  as link_text,link,importance from (select a.template,trim(REGEXP_REPLACE(REGEXP_REPLACE(a.link_text,'schemeid-',' '),'.cms','')) as scheme_id,link,coalesce(a.importance,0)+coalesce(c.importance,0) as importance,scheme_name from  (select a.template,a.link_text,a.importance,link,scheme_name from (select template,link_text,sum(importance) as importance,max(importance) as mx_importance from
(select 'mffactsheet' as template,link,link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%mffactsheet%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)a
inner join
(select 'mffactsheet' as template,link,link_text,REGEXP_REPLACE(scheme_name,'-',' ')as scheme_name
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(2)]) as scheme_name from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%mffactsheet%'
and url not like '%?%'
and url not like '%#%'
and url not like '%m.economictimes.com%'
--and url not like "%/%%"  
 
GROUP BY 1,2
)) group by 1,2,3,4)b on a.link_text=b.link_text and a.mx_importance=b.importance)a
left join
(select template,link_text,sum(importance) as importance,max(importance) as mx_importance from (select 'mffactsheet' as template,link, link_text
,count(*) as importance from (
select url as link,lower(ARRAY_REVERSE(SPLIT(url,'/'))[SAFE_OFFSET(0)]) as link_text from
(SELECT
url,gid
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%mffactsheet%'
and url not like '%?%'
and url not like '%#%'
and url like '%m.economictimes.com%'
 
GROUP BY 1,2
)) group by 1,2,3)
group by 1,2)c on a.link_text=c.link_text
where a.link_text not like '')a
left join
`et-poc-042021.interlink.mf_schemes` b on cast(a.scheme_id as int64)=b.SchemeId and SAFE_CAST(a.scheme_id AS FLOAT64) is not NULL
where SAFE_CAST(a.scheme_id AS FLOAT64) is not NULL
 
union all
select 'marketstats' as template,'' as link_text,url as link,count(*) as importance from (
SELECT
url,gid,count(*)
FROM `et-poc-042021.all_user.EtClientEvents`
WHERE __time >= cast(current_date-30 as timestamp) and client_source='growth_rx' and platform not in ('android','ANDROID','ios','IOS')
and event_name ='page_view'
and page_template like '%marketstats%'
and url like '%https://economictimes.indiatimes.com%'
GROUP BY 1,2
) group by 1,2,3
 
 
)
 
)
where
 
(template='commodity' and row_no<=45)
or (template='company' and row_no<=2600)
or (template='companydefault' and row_no<=2600)
or (template='definition' and row_no<=1000)
or (template='IFSC' and row_no<=500)
or (template='slideshow' and row_no<=50)
or (template='topic' and row_no<=2600)
or (template='storylisting' and row_no<=500)
or (template='mffactsheet' and row_no<=400)
or (template='marketstats' and row_no<=250)
or (template='webstories' and row_no<=50)
or (template='videoshow' and row_no<=50)
or (template='t2-5archivedarticles' and row_no<=30)
or (template='t5-30archivedarticles' and row_no<=30)
or (template='t-30archivedarticles' and row_no<=30)
or (template='primearticle' and row_no<=50)
 
 
 
order by 1,4 desc

~~~
 
 
## Query-10: Objective:To Analyze the user’s watchlist and portfolio addition behavior
 
 ~~~
select case when a.company_name is null then b.company_name else a.company_name end as company_name,
case when a.company_id is null then b.company_id else a.company_id end as company_id,
coalesce(portfolio_users,0) as users_added_company_in_portfolio,
coalesce(watchlist_users,0) as users_added_company_in_watchlist
from
(select company_name,company_id,count(*) as portfolio_users from
(select string_field_1 as email,string_field_2 as company_name,string_field_3 as company_id from  et-poc-042021.Company.watchlist where string_field_1!='\\N' and string_field_1 like '%@%' group by 1,2,3)
group by 1,2)a
full outer join
(select company_name,company_id,count(*) as watchlist_users from
(select email,company_name,company_id from
(SELECT idValue as email,prefDataVal  FROM `et-poc-042021.Company.follow`
where idValue is not null and userSettingSubType in (11) and idValue like '%@%'
group by 1,2)a
inner join (select string_field_3 as company_id,string_field_2 as company_name from
et-poc-042021.Company.watchlist
group by 1,2)b on cast(a.prefDataVal as string)=cast(company_id as string)
 
group by 1,2,3)
group by 1,2) b on a.company_name=b.company_name
~~~ 
 
 
## Query-11: Objective: To Analyze the behavior of loyal free users based on city
~~~
select mon,city,count(*) from
(select a.mon,a.user,city from
(select user,mon,count(*) as sessions from
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,sessionId  from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date,sessionId from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and (((lower(user_subscription_status) not like '%paid%') and (lower(current_subscriber_status) not like '%paid%') and (lower(current_subscriber_status) not like '%active%') and (lower(user_subscription_status) not like '%active%') )
or (current_subscriber_status is null))
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2
having sessions>21)a
left join
(select case when email is null then a.gid else email end as user,city  from
(select gid,city
from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-03-01'
and client_source='growth_rx'
and city is not null and city not in ('NA','Unknown','-')
group by 1,2)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-03-01'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2)b on a.user=b.user
group by 1,2,3) group by 1,2
order by 1,3
~~~

## Query-12: Objective:To Analyze the behavior of loyal free users based on platform wise
 
 ~~~
select mon,platform,count(*) from
(select a.mon,a.user,platform from
(select user,mon,count(*) as sessions from
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,sessionId  from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date,sessionId from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and (((lower(user_subscription_status) not like '%paid%') and (lower(current_subscriber_status) not like '%paid%') and (lower(current_subscriber_status) not like '%active%') and (lower(user_subscription_status) not like '%active%') )
or ((current_subscriber_status is null) or (user_subscription_status is null)))
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2
having sessions>21)a
left join
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,platform  from
(select gid,case when platform in ('web','WEB') and device_category in ('desktop','tablet') then 'desktop'
when platform in ('web','WEB') and device_category not in ('desktop','tablet') then 'mweb'
else lower(platform) end as platform
,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and client_source='growth_rx'
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-03-01'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3)b on a.user=b.user and a.mon=b.mon
group by 1,2,3) group by 1,2
order by 1,2
~~~ 
 
## Query-13: Objective: To Analyze the behavior of loyal free users based on Source wise
 
~~~ 
select mon,source,count(*) from
(select a.mon,a.user,source from
(select user,mon,count(*) as sessions from
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,sessionId  from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date,sessionId from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and (((lower(user_subscription_status) not like '%paid%') and (lower(current_subscriber_status) not like '%paid%') and (lower(current_subscriber_status) not like '%active%') and (lower(user_subscription_status) not like '%active%') )
or ((current_subscriber_status is null) or (user_subscription_status is null)))
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2
having sessions>21)a
left join
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,lower(source) as source  from
(select gid,source
,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and client_source='growth_rx'
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-03-01'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3)b on a.user=b.user and a.mon=b.mon
group by 1,2,3) group by 1,2
order by 1,2
~~~ 
## Query-14: Objective: To Analyze the behavior of loyal free users based on Site section wise excluding app
~~~ 
select mon,site_section,count(*) as users from
(select a.mon,a.user,site_section from
(select user,mon,count(*) as sessions from
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,sessionId  from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date,sessionId from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and (((lower(user_subscription_status) not like '%paid%') and (lower(current_subscriber_status) not like '%paid%') and (lower(current_subscriber_status) not like '%active%') and (lower(user_subscription_status) not like '%active%') )
or ((current_subscriber_status is null) or (user_subscription_status is null)))
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2
having sessions>21)a
inner join
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,lower(site_section) as site_section  from
(select gid,site_section
,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and lower(platform) not in ('android','ios')
and event_name='page_view'
and client_source='growth_rx'
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3)b on a.user=b.user and a.mon=b.mon
group by 1,2,3) group by 1,2
order by 1,3 desc
~~~ 
 
## Query-15: Objective: To Analyze the behavior of loyal free users based on Page Template wise excluding app
 
~~~ 
select mon,page_template,count(*) from
(select a.mon,a.user,page_template from
(select user,mon,count(*) as sessions from
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,sessionId  from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date,sessionId from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and (((lower(user_subscription_status) not like '%paid%') and (lower(current_subscriber_status) not like '%paid%') and (lower(current_subscriber_status) not like '%active%') and (lower(user_subscription_status) not like '%active%') )
or ((current_subscriber_status is null) or (user_subscription_status is null)))
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2
having sessions>21)a
inner join
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,page_template  from
(select gid,page_template
,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-03-01'
and event_name='page_view'
and client_source='growth_rx'
and platform  not in ('ios','android')
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-03-01'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3)b on a.user=b.user and a.mon=b.mon
group by 1,2,3) group by 1,2
order by 1,3 desc
~~~ 
## Query-16: Objective: To Analyze the behavior of loyal free users based on there Events

~~~
select mon,event_category,count(*) from
(select a.mon,a.user,event_category from
(select user,mon,count(*) as sessions from
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,sessionId  from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date,sessionId from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and (((lower(user_subscription_status) not like '%paid%') and (lower(current_subscriber_status) not like '%paid%') and (lower(current_subscriber_status) not like '%active%') and (lower(user_subscription_status) not like '%active%') )
or ((current_subscriber_status is null) or (user_subscription_status is null)))
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2
having sessions>21)a
inner join
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,event_category from
(select gid,event_category
,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date from `et-poc-042021.all_user.EtClientEvents`
where __time>='2021-12-01' and __time<'2022-03-01'
and event_name='event'
and client_source='growth_rx'
and lower(site_section) in ('home','markets','news','industry','wealth','tech','prime','magazines')
 
group by 1,2,3)a
inner join
(select gid,email from `et-poc-042021.all_user.EtClientEvents`
where __time>='2021-12-01' and __time<'2022-03-01'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3)b on a.user=b.user and a.mon=b.mon
group by 1,2,3) group by 1,2
order by 1,3 desc
~~~ 
 
 
## Query-17: Objective: To Analyze the behavior of loyal free users based on there Recency, frequency and Volume 
 
~~~ 
select a.mon,a.user,frequency,recency,volume,sessions from
(select user,mon,count(*) as sessions from
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,sessionId  from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date,sessionId from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and (((lower(user_subscription_status) not like '%paid%') and (lower(current_subscriber_status) not like '%paid%') and (lower(current_subscriber_status) not like '%active%') and (lower(user_subscription_status) not like '%active%') )
or ((current_subscriber_status is null) or (user_subscription_status is null)))
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-02-28 18:30:00'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2
having sessions>21)a
inner join
(select a.mon,a.user,frequency,recency,volume from
(select mon,user,act_day as frequency,DATE_DIFF(cast(concat(extract( year from DATETIME_ADD(cast(mx as datetime), INTERVAL 1 Month )),'-',extract( month from DATETIME_ADD(cast(mx as datetime), INTERVAL 1 Month )),'-','01') as date), mx, DAY) as recency from
(select mon,user,count(*) as act_day,max(c_date) as mx from
(select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,c_date  from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-03-01'
and (((lower(user_subscription_status) not like '%paid%') and (lower(current_subscriber_status) not like '%paid%') and (lower(current_subscriber_status) not like '%active%') and (lower(user_subscription_status) not like '%active%') )
or ((current_subscriber_status is null) or (user_subscription_status is null)))
and client_source='growth_rx'
group by 1,2)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-03-01'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2
))a
left join
(select mon,user,count(*) as volume from (select case when email is null then a.gid else email end as user,extract(Month from c_date) as mon,msid  from
(select gid,msid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-03-01'
and event_name='page_view'
and (url like '%articleshow%' or page_template like '%articleshow%')
and (((lower(user_subscription_status) not like '%paid%') and (lower(current_subscriber_status) not like '%paid%') and (lower(current_subscriber_status) not like '%active%') and (lower(user_subscription_status) not like '%active%') )
or ((current_subscriber_status is null) or (user_subscription_status is null)))
and client_source='growth_rx'
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-12-01' and __time<'2022-03-01'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2)b on a.mon=b.mon and a.user=b.user
where a.user is not null and a.user not in ('-','null'))b on a.mon=b.mon and a.user=b.user
order by 1
~~~ 
 
## Query-18: Objective: To Choose users who should be given free subscription which in return help us in engagement increasing loyalty and also would not target our subscription revenue
 
~~~ 
 
select a.month,a.email,articles_paywalled,syft,Etpay,payu,act_days from
 
(select a.email,a.month,act_days,coalesce(articles_paywalled,0) as articles_paywalled,coalesce(syft,0) as syft,coalesce(Etpay,0) as etpay,coalesce(payu,0) as payu
from
(select a.mon as month,a.email,act_days,articles_paywalled from
 
(select email,mon,count(*) as act_days from
(select email,extract(Month from c_date) as mon,c_date from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-10-01' and __time<'2022-03-29'
and (((lower(user_subscription_status) not like '%paid%') and (lower(current_subscriber_status) not like '%paid%') and (lower(current_subscriber_status) not like '%active%') and (lower(user_subscription_status) not like '%active%') )
or ((current_subscriber_status is null) or (user_subscription_status is null)))
group by 1,2)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-10-01' and __time<'2022-03-29'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2
)a
left join
(select Month,email,sum(articles_paywalled) as articles_paywalled from
(select Month,gid,count(*) as articles_paywalled from
(select gid,msid,extract(Month from cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date)) as Month from `et-poc-042021.all_user.EtClientEvents`
where client_source='growth_rx'
and __time>='2021-10-01' and __time<'2022-03-29'
and ((event_name='page_view'
and (et_product like 'Prime%' or et_product ='Premium')
and device_category in ('desktop','mobile')
and (url like '%articleshow%' or  page_template like '%articleshow%')) or
(event_category in ('Premium Articles PV Tracking') and device_category in ('desktop')) or
(event_category='Articleshow PV Tracking' and device_category in ('desktop','mobile') and et_product='Adaptive Paid') or
(event_name='page_view' and (et_product like 'Prime%' or et_product ='Premium' or et_product='Adaptive Paid')and platform in ('android','ios')) or
(show_paywall_final in ('true','1') and platform in ('android','ios'))
or (paywall_experiment is not null and event_name='page_view'and url like '%articleshow%')
)
and ((lower(user_subscription_status) not like '%paid%') or (lower(current_subscriber_status) not like '%paid%') or (lower(current_subscriber_status) not like '%active%') or (lower(user_subscription_status) not like '%active%') )
and msid is not null
group by 1,2,3) group by 1,2)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where __time>='2021-10-01' and __time<'2022-03-29'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2
)b
 
 
on a.email=b.email and a.mon=b.month)a
 
 
 
 
left join
(select Month,email,sum(syft) as syft from
(select Month,gid,count(*) as syft from (SELECT
 gid,
 CASE
   WHEN event_action IN ('Plan page loaded', 'Flow Started', 'SYFT') THEN 'Plan page loaded'
 ELSE
 event_action
END
 AS event_action,
cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date,
extract(Hour from cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as timestamp)) as hour,
extract(Month from cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date)) as Month
 
FROM
`et-poc-042021.all_user.EtClientEvents`
WHERE
 (event_category='Subscription Flow'
   OR event_category LIKE '%Checkout%')
 AND event_action IN ('Plan Selected',
   'Plan page loaded',
   'Flow Started',
   'SYFT')
 AND gid IS NOT NULL
and __time>='2021-10-01' and __time<'2022-03-29'
GROUP BY
 1,
 2,3,4,5) group by 1,2)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where __time>='2021-10-01' and __time<'2022-03-29'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2)b on a.month=b.month and a.email=b.email
LEFT join
(SELEct
email,
extract(Month from cast(SUBSTR(CAST( datetime(Transaction_Time , 'Asia/Kolkata') AS string), 1,10) as Date)) as Month,
case when status='Incomplete' then 1 else 0 end as Etpay,
case when status='Failed' THEN 1 else 0 end as payu
from `et-poc-042021.user_target.6month_ETPAY`
where status in ('Incomplete','Failed')
and Transaction_Time>'2021-10-01' and Transaction_Time<'2022-03-29'
group by 1,2,3,4
)c on a.month=c.month and a.email=c.email) a
 
left join
(select user_email from (SELECT user_subscription_account_id FROM et-poc-042021.all_user.user_subscriptions
where user_billing_transaction_plan_id not in (7,6,25)
and user_billing_transaction_bill_transaction_source not in ('OFFLINE')
and product_code in ('ETPR')
group by 1)a
inner join
et-poc-042021.all_user.user_subscription_accounts b
on a.user_subscription_account_id=SUBSTR(SUBSTR(b._id,10),1,LENGTH(SUBSTR(b._id,10))-1)
group by 1) b  on a.email=b.user_email
where user_email is null
group by 1,2,3,4,5,6,7
 
~~~ 
 
## Query-19: Objective: Timesprime expired users analysis
 
~~~ 
select user_email,act_days,articles from
(select user_email from
`et-poc-042021.user_target.Times_prime_expired` a inner join et-poc-042021.all_user.user_subscription_accounts b
on a.string_field_0=SUBSTR(SUBSTR(b._id,10),1,LENGTH(SUBSTR(b._id,10))-1)
group by 1 )a
left join
(select email,count(*) as act_days from
(select email,extract(Month from c_date) as mon,c_date from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2022-02-01' and __time<'2022-03-29'
 
group by 1,2)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where   __time>='2022-02-01' and __time<'2022-03-29'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1)b on a.user_email=b.email
 
left join
(select email,sum(articles) as articles from
(select email,mon,count(*) as articles from
(select email,extract(Month from c_date) as mon,msid from
(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata') AS string), 1,10) as Date) as c_date,msid from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2022-02-01' and __time<'2022-03-29'
and url like '%articleshow%'
and event_name='page_view'
 
group by 1,2,3)a
inner join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where   __time>='2022-02-01' and __time<'2022-03-29'
and __time<cast(current_date() as timestamp) and gid is not null and email is not null and
gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )b
on a.gid=b.gid
group by 1,2,3) group by 1,2) group by 1)c on a.user_email=c.email
~~~ 
 
## Query-20: Objective: Queries to analyze the impact of migration of portfolio db to follow db
 
~~~ 
select email from
(select f.email,prefDataVal from
(select a.email,prefDataVal from (SELECT idValue as email,prefDataVal  FROM `et-poc-042021.Company.follow`
where idValue is not null and userSettingSubType in (11) and idValue like '%@%'
group by 1,2)a
inner join
(select string_field_1 as email from
(select string_field_1 from  et-poc-042021.Company.watchlist where string_field_1!='\\N' and string_field_1 like '%@%' group by 1)a
inner join
(SELECT idValue as email  FROM `et-poc-042021.Company.follow`
where idValue is not null and userSettingSubType in (11) and idValue like '%@%'
group by 1)b on lower(b.email)=lower(a.string_field_1)
group by 1)b on lower(a.email)=lower(b.email)
group by 1,2)f
left join
(select a.email,company_id from
(select string_field_1 as email,string_field_3 as company_id from  et-poc-042021.Company.watchlist where string_field_1!='\\N' and string_field_1 like '%@%' group by 1,2)a
inner join
(select string_field_1 as email from
(select string_field_1 from  et-poc-042021.Company.watchlist where string_field_1!='\\N' and string_field_1 like '%@%' group by 1)a
inner join
(SELECT idValue as email  FROM `et-poc-042021.Company.follow`
where idValue is not null and userSettingSubType in (11) and idValue like '%@%'
group by 1)b on lower(b.email)=lower(a.string_field_1)
group by 1)b on lower(a.email)=lower(b.email)
 
group by 1,2
)c on lower(f.email)=lower(c.email) and prefDataVal=company_id
where company_id is null
group by 1,2)
group by 1
~~~ 
 
~~~
select stocks_added,count(*) from (select primaryEmail,count(*) as stocks_added from
(select primaryEmail,prefDataVal from
(select primaryEmail,prefDataVal from (SELECT primaryEmail,prefDataVal  FROM `et-poc-042021.Company.follow`
where primaryEmail is not null and userSettingSubType in (5,11) and primaryEmail like '%@%'
group by 1,2)a
inner join
(select string_field_1 as email from
(select string_field_1 from  et-poc-042021.Company.watchlist where string_field_1!='\\N' and string_field_1 like '%@%' group by 1)a
inner join
(SELECT primaryEmail  FROM `et-poc-042021.Company.follow`
where primaryEmail is not null and userSettingSubType in (5,11) and primaryEmail like '%@%'
group by 1)b on b.primaryEmail=a.string_field_1
group by 1)b on a.primaryEmail=b.email
group by 1,2)f
left join
(select a.email,company_id from
(select string_field_1 as email,string_field_3 as company_id from  et-poc-042021.Company.watchlist where string_field_1!='\\N' and string_field_1 like '%@%' group by 1,2)a
inner join
(select string_field_1 as email from
(select string_field_1 from  et-poc-042021.Company.watchlist where string_field_1!='\\N' and string_field_1 like '%@%' group by 1)a
inner join
(SELECT primaryEmail  FROM `et-poc-042021.Company.follow`
where primaryEmail is not null and userSettingSubType in (5,11) and primaryEmail like '%@%'
group by 1)b on b.primaryEmail=a.string_field_1
group by 1)b on a.email=b.email

group by 1,2
)c on f.primaryEmail=c.email and prefDataVal=company_id
where company_id is null
group by 1,2)
group by 1) group by 1
order by 1 asc

~~~
~~~
select stock_added,count(*) as users from
(select primaryEmail,count(*) as stock_added from
(select primaryEmail,prefDataVal from (SELECT primaryEmail,prefDataVal  FROM `et-poc-042021.Company.follow`
where primaryEmail is not null and userSettingSubType in (5,11) and primaryEmail like '%@%'
group by 1,2)a
left join
(select string_field_1 as email from
(select string_field_1 from  et-poc-042021.Company.watchlist where string_field_1!='\\N' and string_field_1 like '%@%' group by 1)a
inner join
(SELECT primaryEmail  FROM `et-poc-042021.Company.follow`
where primaryEmail is not null and userSettingSubType in (5,11) and primaryEmail like '%@%'
group by 1)b on b.primaryEmail=a.string_field_1
group by 1)b on a.primaryEmail=b.email
where email is null
group by 1,2)
group by 1) group by 1
order by 1

~~~

## Query-21: Objective: Query Used to analyze the traffic on homepage and how many free and paid users are reading articles from home page
~~~
select mon,last_click_source,user_type,count(*) as users,sum(Total_articles_read) as Total_articles_read from
(select extract(Month from c_date) as mon,user,user_type,last_click_source,sum(volume) as Total_articles_read from
(select user,user_type,c_date,last_click_source,count(*) as volume from
(select case when email is null then a.gid else email end as user,user_type,msid,c_date,last_click_source from

(select gid,cast(SUBSTR(CAST( datetime(__time , 'Asia/Kolkata')  AS string), 1,10) as Date) as c_date,msid,
case when (lower(user_subscription_status) like '%paid%' or lower(current_subscriber_status) like '%paid%' or lower(current_subscriber_status) like '%active%' or lower(user_subscription_status) like '%active%' ) then 'paid'
else 'free' end as user_type,last_click_source
from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-11-30 18:30:00' and __time<'2022-02-28 18:30:00' and event_name='page_view'
and (url like '%articleshow%' or page_template like '%articleshow%')
--and last_click_source='homepage'
and ((lower(platform) in ('desktop')) or (lower(platform) in ('web') and device_category in ('desktop')))
group by 1,2,3,4,5)a
left join
(select gid,email  from `et-poc-042021.all_user.EtClientEvents`
where  __time>='2021-11-30 18:30:00' and __time<'2022-02-28 18:30:00' and gid is not null and email is not null
and gid not in ('00000000-0000-0000-0000-000000000000') group by 1,2 )e
on e.gid=a.gid
group by 1,2,3,4,5)
group by 1,2,3,4) group by 1,2,3,4)
where mon=1
group by 1,2,3
order by 1,2,4

~~~



## Query-22: Objective: Query Used to analyze engagement of paid users on App

~~~
select 
  ar.month, 
  ar.platform, 
  Free, 
  prime, 
  premium, 
  mail_sent, 
  mail_open, 
  mail_click, 
  company, 
  video, 
  commodity, 
  slideshow, 
  stock_report 
from 
  (
    select 
      month, 
      platform, 
      sum(premium) as premium, 
      sum(prime) as prime, 
      sum(Free) as Free 
    from 
      (
        select 
          month, 
          email, 
          platform, 
          sum(
            case when et_product = 'Premium' then article_read else 0 end
          ) as premium, 
          sum(
            case when et_product = 'Prime Plus' then article_read else 0 end
          ) as prime, 
          sum(
            case when et_product = 'Free' then article_read else 0 end
          ) as Free 
        from 
          (
            select 
              month, 
              platform, 
              email, 
              et_product, 
              count(*) as article_read 
            from 
              (
                select 
                  month, 
                  platform, 
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
                              CAST(__time AS string), 
                              1, 
                              10
                            ) as Date
                          )
                      ) as month, 
                      gid, 
                      lower(platform) as platform, 
                      case when et_product = 'Premium' then 'Premium' when et_product = 'Prime Plus' then 'Prime Plus' else 'Free' end as et_product, 
                      msid 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= '2021-11-01' 
                      and __time < '2022-02-28 18:30:00' 
                      and lower(platform) in ('android', 'ios') 
                      and (
                        lower(user_subscription_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%paid%' 
                        or lower(current_subscriber_status) like '%active%' 
                        or lower(user_subscription_status) like '%active%'
                      ) 
                      and (
                        (
                          (
                            url like '%articleshow%' 
                            or page_template like '%articleshow%'
                          ) 
                          and event_name = 'page_view' 
                          and et_product not in (
                            'Behind Sign In', 'Advertorial', 
                            'homepage'
                          )
                        ) 
                        or (
                          event_category in (
                            'Premium Articles PV Tracking', 
                            'Articleshow PV Tracking'
                          )
                        )
                      ) 
                    group by 
                      1, 
                      2, 
                      3, 
                      4, 
                      5
                  ) a 
                  inner join (
                    select 
                      gid, 
                      email 
                    from 
                      `et-poc-042021.all_user.EtClientEvents` 
                    where 
                      __time >= '2021-11-01' 
                      and __time < '2022-02-28 18:30:00' 
                      and gid is not null 
                      and email is not null 
                    group by 
                      1, 
                      2
                  ) e on e.gid = a.gid 
                group by 
                  1, 
                  2, 
                  3, 
                  4, 
                  5
              ) 
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
  ) ar 
  left join (
    select 
      month, 
      platform, 
      sum(mail_sent) as mail_sent, 
      sum(mail_open) as mail_open, 
      sum(mail_click) as mail_click 
    from 
      (
        select 
          o.month, 
          o.email, 
          mail_sent, 
          mail_open, 
          mail_click, 
          platform 
        from 
          (
            select 
              extract(
                month 
                from 
                  cast(
                    SUBSTR(
                      CAST(__time AS string), 
                      1, 
                      10
                    ) as Date
                  )
              ) as month, 
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
              __time >= '2021-11-01' 
              and __time < '2022-02-28 18:30:00' 
              and client_source = 'et_newsletter' 
              and mailer_type in ('NEWSLETTER', 'PROMOTIONAL') 
              and nl_name not like '%Test%' 
              and event_name in (
                'mail_sent', 'mail_open', 'mail_click'
              ) 
            group by 
              1, 
              2
          ) o 
          inner join (
            select 
              extract(
                month 
                from 
                  cast(
                    SUBSTR(
                      CAST(__time AS string), 
                      1, 
                      10
                    ) as Date
                  )
              ) as month, 
              email, 
              lower(platform) as platform 
            from 
              `et-poc-042021.all_user.EtClientEvents` 
            where 
              __time >= '2021-11-01' 
              and __time < '2022-02-28 18:30:00' 
              and gid is not null 
              and email is not null 
              and (
                lower(user_subscription_status) like '%paid%' 
                or lower(current_subscriber_status) like '%paid%' 
                or lower(current_subscriber_status) like '%active%' 
                or lower(user_subscription_status) like '%active%'
              ) 
              and lower(platform) in ('android', 'ios') 
            group by 
              1, 
              2, 
              3
          ) e on o.email = e.email 
          and o.month = e.month
      ) 
    group by 
      1, 
      2
  ) o on o.month = ar.month 
  and o.platform = ar.platform 
  left join (
    select 
      month, 
      platform, 
      sum(cnt) as company 
    from 
      (
        select 
          platform, 
          month, 
          email, 
          count(*) as cnt 
        from 
          (
            select 
              month, 
              platform, 
              email, 
              url 
            from 
              (
                select 
                  extract(
                    month 
                    from 
                      cast(
                        SUBSTR(
                          CAST(__time AS string), 
                          1, 
                          10
                        ) as Date
                      )
                  ) as month, 
                  gid, 
                  url, 
                  lower(platform) as platform 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2021-11-01' 
                  and __time < '2022-02-28 18:30:00' 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                  and (
                    url like '%/company/%' 
                    and url not like '%articleshow%' 
                    and lower(platform) in ('ios', 'android')
                  ) 
                  and event_name = 'page_view' 
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
                  __time >= '2021-11-01' 
                  and __time < '2022-02-28 18:30:00' 
                  and gid is not null 
                  and email is not null 
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
          2, 
          3
      ) 
    group by 
      1, 
      2
  ) a on ar.month = a.month 
  and ar.platform = a.platform 
  left join (
    select 
      month, 
      platform, 
      sum(cnt) as video 
    from 
      (
        select 
          month, 
          email, 
          platform, 
          count(*) as cnt 
        from 
          (
            select 
              month, 
              email, 
              url, 
              platform 
            from 
              (
                select 
                  extract(
                    month 
                    from 
                      cast(
                        SUBSTR(
                          CAST(__time AS string), 
                          1, 
                          10
                        ) as Date
                      )
                  ) as month, 
                  gid, 
                  url, 
                  lower(platform) as platform 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2021-11-01' 
                  and __time < '2022-02-28 18:30:00' 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                  and (
                    url like '%videoshow%' 
                    and event_name = 'page_view'
                  ) 
                  and lower(platform) in ('ios', 'android') 
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
                  __time >= '2021-11-01' 
                  and __time < '2022-02-28 18:30:00' 
                  and gid is not null 
                  and email is not null 
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
          2, 
          3
      ) 
    group by 
      1, 
      2
  ) b on ar.month = b.month 
  and ar.platform = b.platform 
  left join (
    select 
      month, 
      platform, 
      sum(cnt) as commodity 
    from 
      (
        select 
          month, 
          email, 
          platform, 
          count(*) as cnt 
        from 
          (
            select 
              month, 
              email, 
              url, 
              platform 
            from 
              (
                select 
                  extract(
                    month 
                    from 
                      cast(
                        SUBSTR(
                          CAST(__time AS string), 
                          1, 
                          10
                        ) as Date
                      )
                  ) as month, 
                  gid, 
                  url, 
                  lower(platform) as platform 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2021-11-01' 
                  and __time < '2022-02-28 18:30:00' 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                  and (
                    page_template like '%commodity%' 
                    and event_name = 'page_view'
                  ) 
                  and lower(platform) in ('ios', 'android') 
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
                  __time >= '2021-11-01' 
                  and __time < '2022-02-28 18:30:00' 
                  and gid is not null 
                  and email is not null 
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
          2, 
          3
      ) 
    group by 
      1, 
      2
  ) c on ar.month = c.month 
  and ar.platform = c.platform 
  left join (
    select 
      month, 
      platform, 
      sum(cnt) as slideshow 
    from 
      (
        select 
          month, 
          platform, 
          email, 
          count(*) as cnt 
        from 
          (
            select 
              month, 
              email, 
              url, 
              platform 
            from 
              (
                select 
                  extract(
                    month 
                    from 
                      cast(
                        SUBSTR(
                          CAST(__time AS string), 
                          1, 
                          10
                        ) as Date
                      )
                  ) as month, 
                  gid, 
                  url, 
                  lower(platform) as platform 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2021-11-01' 
                  and __time < '2022-02-28 18:30:00' 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                  and (
                    url like '%slideshow%' 
                    and event_name = 'page_view'
                  ) 
                  and lower(platform) in ('ios', 'android') 
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
                  __time >= '2021-11-01' 
                  and __time < '2022-02-28 18:30:00' 
                  and gid is not null 
                  and email is not null 
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
          2, 
          3
      ) 
    group by 
      1, 
      2
  ) d on ar.month = d.month 
  and ar.platform = d.platform 
  left join (
    select 
      month, 
      platform, 
      sum(cnt) as stock_report 
    from 
      (
        select 
          month, 
          email, 
          platform, 
          count(*) as cnt 
        from 
          (
            select 
              month, 
              email, 
              url, 
              platform 
            from 
              (
                select 
                  extract(
                    month 
                    from 
                      cast(
                        SUBSTR(
                          CAST(__time AS string), 
                          1, 
                          10
                        ) as Date
                      )
                  ) as month, 
                  gid, 
                  url, 
                  lower(platform) as platform 
                from 
                  `et-poc-042021.all_user.EtClientEvents` 
                where 
                  __time >= '2021-11-01' 
                  and __time < '2022-02-28 18:30:00' 
                  and (
                    lower(user_subscription_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%paid%' 
                    or lower(current_subscriber_status) like '%active%' 
                    or lower(user_subscription_status) like '%active%'
                  ) 
                  and et_product in ('Stock Report') 
                  and event_name = 'page_view' 
                  and page_template = 'Stock Report Viewer' 
                  and lower(platform) in ('ios', 'android') 
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
                  __time >= '2021-11-01' 
                  and __time < '2022-02-28 18:30:00' 
                  and gid is not null 
                  and email is not null 
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
          2, 
          3
      ) 
    group by 
      1, 
      2
  ) e on ar.month = e.month 
  and ar.platform = e.platform

~~~
