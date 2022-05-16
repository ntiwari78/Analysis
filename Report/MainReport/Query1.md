~~~
SELECT
	*
EXCEPT
(properties),
CASE
	WHEN properties = 'mail_sent' THEN 'Sent'
	WHEN properties = 'mail_open' THEN 'Open'
	WHEN properties = 'mail_click' THEN 'Click'
	WHEN properties = 'users' THEN 'Users'
	WHEN properties = 'open_percent' THEN 'Open Rate'
	WHEN properties = 'click_open_percent' THEN 'Click To Open Rate'
END AS properties,
CASE
	WHEN properties = 'mail_sent' THEN 1
	WHEN properties = 'mail_open' THEN 2
	WHEN properties = 'mail_click' THEN 3
	WHEN properties = 'users' THEN 4
	WHEN properties = 'open_percent' THEN 5
	WHEN properties = 'click_open_percent' THEN 6
END AS prop_id
FROM
(
SELECT
	*
FROM
	(
	SELECT
		nl_name,
		mon,
		val,
		properties
	FROM
		(
		SELECT
			*,
			CASE
				WHEN mail_sent = 0 THEN 0
				ELSE round(
CAST(mail_open AS float64)/ CAST(mail_sent AS float64)* 100,
				2
)
			END AS open_percent,
			CASE
				WHEN mail_open = 0 THEN 0
				ELSE round(
CAST(mail_click AS float64)/ CAST(mail_open AS float64)* 100,
				2
)
			END AS click_open_percent
		FROM
			(
			SELECT
				a.nl_name,
				CASE
					WHEN c_date = current_date - 2 THEN 'D2'
					WHEN c_date = current_date - 3 THEN 'D3'
					WHEN c_date = current_date - 4 THEN 'D4'
				END AS mon,
				sum(mail_sent) AS mail_sent,
				sum(mail_open) AS mail_open,
				sum(mail_click) AS mail_click,
				sum(users) AS users
			FROM
				(
				SELECT
					CAST(
SUBSTR(
CAST(
datetime(__time,
					'Asia/Kolkata') AS string
),
					1,
					10
) AS Date
) AS c_date,
					CASE
						WHEN nl_name = 'News Digest'
						AND tags IN (
'rejected_new_mailer', 'Rejected'
) THEN 'News Digest Static'
						WHEN nl_name = 'News Digest'
						AND tags NOT IN (
'rejected_new_mailer', 'Rejected'
) THEN 'News Digest ML Generated'
						WHEN nl_name = 'ET RISE Biz Listings' THEN 'ET RISE'
						ELSE nl_name
					END AS nl_name,
					nl_schedule_id,
					count(*) AS mail_sent
				FROM
					`et-poc-042021. all_user.EtClientEvents`
				WHERE
					__time >= CAST(
DATETIME_ADD(
DATETIME_ADD(
CAST(current_date - 5 AS datetime),
					INTERVAL 18 HOUR
),
					INTERVAL 30 MINUTE
) AS timestamp
)
					AND __time < CAST(
DATETIME_ADD(
DATETIME_ADD(
CAST(current_date - 2 AS datetime),
					INTERVAL 18 HOUR
),
					INTERVAL 30 MINUTE
) AS timestamp
)
					AND client_source = 'et_newsletter'
					AND event_name IN ('mail_sent')
					AND nl_name NOT LIKE '%Test%'
					AND (
mailer_type IN ('NEWSLETTER')
						OR nl_name IN ('News Digest')
)
					AND nl_name NOT IN (
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
			LEFT JOIN (
				SELECT
					nl_name,
					nl_schedule_id,
					sum(mail_open) AS mail_open,
					sum(mail_click) AS mail_click,
					count(*) AS users
				FROM
					(
					SELECT
						CASE
							WHEN nl_name = 'News Digest'
								AND tags IN (
'rejected_new_mailer', 'Rejected'
) THEN 'News Digest Static'
								WHEN nl_name = 'News Digest'
									AND tags NOT IN (
'rejected_new_mailer', 'Rejected'
) THEN 'News Digest ML Generated'
									WHEN nl_name = 'ET RISE Biz Listings' THEN 'ET RISE'
									ELSE nl_name
								END AS nl_name,
								email,
								nl_schedule_id,
								sum(
CASE WHEN event_name = 'mail_open' THEN 1 ELSE 0 END
) AS mail_open,
								sum(
CASE WHEN event_name = 'mail_click' THEN 1 ELSE 0 END
) AS mail_click
							FROM
								`et-poc-042021. all_user.EtClientEvents`
							WHERE
								__time > CAST(
DATETIME_ADD(
DATETIME_ADD(
CAST(current_date - 5 AS datetime),
								INTERVAL 18 HOUR
),
								INTERVAL 30 MINUTE
) AS timestamp
)
									AND client_source = 'et_newsletter'
									AND event_name IN ('mail_open', 'mail_click')
										AND nl_name NOT LIKE '%Test%'
										AND (
mailer_type IN ('NEWSLETTER')
											OR nl_name IN ('News Digest')
)
											AND nl_name NOT IN (
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
				GROUP BY
					1,
					2
) b ON
				a.nl_schedule_id = b.nl_schedule_id
				AND CAST(a.nl_name AS string)= CAST(b.nl_name AS string)
			GROUP BY
				1,
				2
)
		WHERE
			mail_sent > 5
	UNION ALL
		SELECT
			nl_name,
			'last_7_days' AS mon,
			CAST(
round(
CAST(mail_sent AS float64)/ CAST(DAY AS float64),
			0
) AS float64
) AS mail_sent,
			CAST(
round(
CAST(mail_open AS float64)/ CAST(DAY AS float64),
			0
) AS float64
) AS mail_open,
			CAST(
round(
CAST(mail_click AS float64)/ CAST(DAY AS float64),
			0
) AS float64
) AS mail_click,
			CAST(
round(
CAST(users AS float64)/ CAST(DAY AS float64),
			0
) AS float64
) AS users,
			CASE
				WHEN mail_sent = 0 THEN 0
				ELSE round(
CAST(mail_open AS float64)/ CAST(mail_sent AS float64)* 100,
				2
)
			END AS open_percent,
			CASE
				WHEN mail_open = 0 THEN 0
				ELSE round(
CAST(mail_click AS float64)/ CAST(mail_open AS float64)* 100,
				2
)
			END AS click_open_percent
		FROM
			(
			SELECT
				nl_name,
				sum(mail_sent) AS mail_sent,
				sum(mail_open) AS mail_open,
				sum(mail_click) AS mail_click,
				sum(users) AS users,
				sum(DAY) AS DAY
			FROM
				(
				SELECT
					nl_name,
					CASE
						WHEN mail_sent != 0 THEN 1
						ELSE 0
					END AS DAY,
					mail_sent,
					mail_open,
					mail_click,
					users
				FROM
					(
					SELECT
						c_date,
						nl_name,
						sum(mail_sent) AS mail_sent,
						sum(mail_open) AS mail_open,
						sum(mail_click) AS mail_click,
						sum(
CASE WHEN mail_open != 0
OR mail_click != 0 THEN 1 ELSE 0 END
) AS users
					FROM
						(
						SELECT
							CAST(
SUBSTR(
CAST(
datetime(__time,
							'Asia/Kolkata') AS string
),
							1,
							10
) AS Date
) AS c_date,
							CASE
								WHEN nl_name = 'News Digest'
									AND tags IN (
'rejected_new_mailer', 'Rejected'
) THEN 'News Digest Static'
									WHEN nl_name = 'News Digest'
										AND tags NOT IN (
'rejected_new_mailer', 'Rejected'
) THEN 'News Digest ML Generated'
										WHEN nl_name = 'ET RISE Biz Listings' THEN 'ET RISE'
										ELSE nl_name
									END AS nl_name,
									email,
									sum(
CASE WHEN event_name = 'mail_sent' THEN 1 ELSE 0 END
) AS mail_sent,
									sum(
CASE WHEN event_name = 'mail_open' THEN 1 ELSE 0 END
) AS mail_open,
									sum(
CASE WHEN event_name = 'mail_click' THEN 1 ELSE 0 END
) AS mail_click
								FROM
									`et-poc-042021. all_user.EtClientEvents`
								WHERE
									__time >= CAST(current_date - 7 AS timestamp)
										AND client_source = 'et_newsletter'
										AND event_name IN (
'mail_sent', 'mail_open', 'mail_click'
)
											AND nl_name NOT LIKE '%Test%'
											AND (
mailer_type IN ('NEWSLETTER')
												OR nl_name IN ('News Digest')
)
												AND nl_name NOT IN (
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
					GROUP BY
						1,
						2
)
)
			GROUP BY
				1
)
		WHERE
			mail_sent != 0
	UNION ALL
		SELECT
			a.nl_name,
			a.mon,
			CASE
				WHEN DAY = 0 THEN 0
				ELSE CAST(
round(
CAST(mail_sent AS float64)/ CAST(DAY AS float64),
				0
) AS float64
)
			END AS mail_sent,
			CASE
				WHEN DAY = 0 THEN 0
				ELSE CAST(
round(
CAST(mail_open AS float64)/ CAST(DAY AS float64),
				0
) AS float64
)
			END AS mail_open,
			CASE
				WHEN DAY = 0 THEN 0
				ELSE CAST(
round(
CAST(mail_click AS float64)/ CAST(DAY AS float64),
				0
) AS float64
)
			END AS mail_click,
			CASE
				WHEN DAY = 0 THEN 0
				ELSE CAST(
round(
CAST(users AS float64)/ CAST(DAY AS float64),
				0
) AS float64
)
			END AS users,
			CASE
				WHEN mail_sent = 0 THEN 0
				ELSE round(
CAST(mail_open AS float64)/ CAST(mail_sent AS float64)* 100,
				2
)
			END AS open_percent,
			CASE
				WHEN mail_sent = 0 THEN 0
				ELSE round(
CAST(mail_click AS float64)/ CAST(mail_open AS float64)* 100,
				2
)
			END AS click_open_percent
		FROM
			(
			SELECT
				nl_name,
				CASE
					WHEN mon = EXTRACT(
MONTH
				FROM
					DATE_SUB(
Date(
EXTRACT(
YEAR
				FROM
					current_date()
),
					EXTRACT(
MONTH
				FROM
					current_date()
),
					01
),
					INTERVAL 1 MONTH
)
) THEN 'M1'
					WHEN mon = EXTRACT(
MONTH
				FROM
					DATE_SUB(
Date(
EXTRACT(
YEAR
				FROM
					current_date()
),
					EXTRACT(
MONTH
				FROM
					current_date()
),
					01
),
					INTERVAL 2 MONTH
)
) THEN 'M2'
					WHEN mon = EXTRACT(
MONTH
				FROM
					DATE_SUB(
Date(
EXTRACT(
YEAR
				FROM
					current_date()
),
					EXTRACT(
MONTH
				FROM
					current_date()
),
					01
),
					INTERVAL 3 MONTH
)
) THEN 'M3'
					WHEN mon = EXTRACT(
MONTH
				FROM
					current_date()
) THEN 'MTD'
				END AS mon,
				sum(mail_sent) AS mail_sent,
				sum(mail_open) AS mail_open,
				sum(mail_click) AS mail_click,
				sum(users) AS users,
				sum(DAY) AS DAY
			FROM
				(
				SELECT
					nl_name,
					mon,
					CASE
						WHEN mail_sent != 0 THEN 1
						ELSE 0
					END AS DAY,
					mail_sent,
					mail_open,
					mail_click,
					users
				FROM
					(
					SELECT
						nl_name,
						mon,
						c_date,
						sum(mail_sent) AS mail_sent,
						sum(mail_open) AS mail_open,
						sum(mail_click) AS mail_click,
						sum(
CASE WHEN mail_open != 0
OR mail_click != 0 THEN 1 ELSE 0 END
) AS users
					FROM
						(
						SELECT
							EXTRACT(
MONTH
						FROM
							CAST(
SUBSTR(
CAST(
datetime(__time,
							'Asia/Kolkata') AS string
),
							1,
							10
) AS Date
)
) AS mon,
							CAST(
SUBSTR(
CAST(
datetime(__time,
							'Asia/Kolkata') AS string
),
							1,
							10
) AS Date
) AS c_date,
							CASE
								WHEN nl_name = 'News Digest'
									AND tags IN (
'rejected_new_mailer', 'Rejected'
) THEN 'News Digest Static'
									WHEN nl_name = 'News Digest'
										AND tags NOT IN (
'rejected_new_mailer', 'Rejected'
) THEN 'News Digest ML Generated'
										WHEN nl_name = 'ET RISE Biz Listings' THEN 'ET RISE'
										ELSE nl_name
									END AS nl_name,
									email,
									sum(
CASE WHEN event_name = 'mail_sent' THEN 1 ELSE 0 END
) AS mail_sent,
									sum(
CASE WHEN event_name = 'mail_open' THEN 1 ELSE 0 END
) AS mail_open,
									sum(
CASE WHEN event_name = 'mail_click' THEN 1 ELSE 0 END
) AS mail_click
								FROM
									`et-poc-042021. all_user.EtClientEvents`
								WHERE
									__time >= CAST (
DATE_SUB(
Date(
EXTRACT(
YEAR
								FROM
									current_date()
),
									EXTRACT(
MONTH
								FROM
									current_date()
),
									01
),
									INTERVAL 3 MONTH
) AS timestamp
)
									AND client_source = 'et_newsletter'
									AND event_name IN (
'mail_sent', 'mail_open', 'mail_click'
)
										AND nl_name NOT LIKE '%Test%'
										AND (
mailer_type IN ('NEWSLETTER')
											OR nl_name IN ('News Digest')
)
											AND nl_name NOT IN (
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
					GROUP BY
						1,
						2,
						3
)
)
			GROUP BY
				1,
				2
) a
) unpivot(
val FOR properties IN (
mail_sent, mail_open, mail_click,
users, open_percent, click_open_percent
)
) AS upv
) pivot (
sum(val) FOR mon IN (
'D2', 'D3', 'D4', 'last_7_days', 'MTD',
'M1', 'M2', 'M3'
)
)
)
~~~
