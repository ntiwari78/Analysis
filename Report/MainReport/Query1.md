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
