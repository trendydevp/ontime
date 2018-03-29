# Sample Queries

## Airports

### Number of Flights
	
	SELECT top 100 q_origin.origin AS airport,
	           q_origin.flights,
	           q_origin.flights * 100 / q_all.flights AS percent
	FROM
	  (SELECT origin, count(*) AS flights
	   FROM ontime GROUP BY origin) q_origin
	
	JOIN (SELECT count(*) AS flights FROM ontime) q_all
	
	ON q_origin.origin != ''
	
	ORDER BY percent DESC, q_origin.flights DESC

### Number of Flights for Year

	SELECT top 100 q_origin.origin AS airport,
	           q_origin.origincityname AS city,
	           q_origin.flights,
	           q_origin.flights*100/q_all.flights AS percent
	FROM
	  (SELECT year,
	          origin,
	          origincityname,
	          count(*) AS flights
	   FROM ontime
	   WHERE year = 2016
	   GROUP BY year, origin, origincityname) q_origin
	JOIN
	  (SELECT year, count(*) AS flights
	   FROM ontime
	   WHERE YEAR = 2016
	   GROUP BY YEAR) q_all 
	ON q_origin.year=q_all.year
	
	ORDER BY percent DESC, q_origin.flights DESC

### Number of Delays (more than 10 minutes)

	SELECT t.origin,
	       t.origincityname,
	       delays,
	       total_flights,
	       delays * 100 / total_flights AS percent
	FROM
	  (SELECT origin, origincityname, count(*) AS delays FROM ontime
	   WHERE DepDelay > 10 GROUP BY origin, origincityname) t
	
	JOIN
	  (SELECT origin, count(*) AS total_flights
	   FROM ontime GROUP BY origin) t2 ON (t.origin=t2.origin)
	
	WHERE delays > 10
	
	ORDER BY percent DESC

### Number of Delays (more than 60 minutes)

	SELECT t.origin,
	       t.origincityname,
	       delays,
	       total_flights,
	       delays * 100 / total_flights AS percent
	FROM
	  (SELECT origin, origincityname, count(*) AS delays FROM ontime
	   WHERE DepDelay > 60 GROUP BY origin, origincityname) t
	
	JOIN
	  (SELECT origin, count(*) AS total_flights
	   FROM ontime GROUP BY origin) t2 ON (t.origin=t2.origin)
	
	WHERE delays > 10
	
	ORDER BY percent DESC

### Number of Delays for Year (more than 10 minutes

	SELECT t.origin,
	       t.origincityname,
	       delays,
	       total_flights,
	       delays * 100 / total_flights AS percent
	FROM
	  (SELECT origin, origincityname, count(*) AS delays FROM ontime
	   WHERE DepDelay > 10 GROUP BY origin, origincityname) t
	
	JOIN
	  (SELECT origin, count(*) AS total_flights
	   FROM ontime GROUP BY origin) t2 ON (t.origin=t2.origin)
	
	WHERE delays > 10
	
	ORDER BY percent DESC

### Number of Destinations

    SELECT top 100 origin, origincityname, count(distinct dest) as destination_count
    FROM ontime WHERE year = 2017 group by 1, 2 order by 3 desc;

## Flights

### Number of Flights per Year
	
	SELECT year, count(*) as flights
	FROM ontime
	GROUP BY year
	ORDER BY year DESC

### Average Flight Duration per Year

    SELECT year, avg(actualelapsedtime) / 60 as hours
    FROM ontime
    GROUP BY year
    ORDER BY year DESC
    
### Average Flight Distance per Year

    SELECT year, avg(distance) as distance
    FROM ontime
    GROUP BY year
    ORDER BY year DESC

## Flight Code/Numbers

### Most Delays

    SELECT FIRST 100 t.carrier as carriercode, c.carrier,
           t.flightnum,
           delays,
           total_flights,
           delays * 100 / total_flights AS percent
    FROM
      (SELECT carrier, flightnum, count(*) AS delays FROM ontime
       WHERE DepDelay > 10 GROUP BY flightnum, carrier) t
    
    JOIN
      (SELECT flightnum, count(*) AS total_flights
       FROM ontime GROUP BY flightnum) t2 ON (t.flightnum=t2.flightnum)
    
    LEFT JOIN (SELECT carrier, ccode FROM carriers) c ON (t.carrier = c.ccode)
    
    WHERE total_flights > 50
    
    ORDER BY percent DESC

## Carriers

### Number of Airports

    SELECT TOP 100 ontime.carrier as ccode, c.carrier, count(distinct origin) as airports
    FROM ontime
    LEFT JOIN (SELECT ccode, carrier FROM carriers) c ON ontime.carrier = c.ccode
    GROUP BY ontime.carrier, c.carrier
    ORDER BY airports DESC

### Number of Flights

    SELECT TOP 100 ontime.carrier, c.carrier, count(*) as flights
    FROM ontime
    LEFT JOIN (SELECT ccode, carrier FROM carriers) c ON ontime.carrier = c.ccode
    GROUP BY ontime.carrier, c.carrier
    ORDER BY flights DESC

### Most Delays (in percent of their total flights)
		
	SELECT t.carrier as ccode,
		c.carrier,
		delays,
		total_flights,
		delays * 100 / total_flights AS percent
	FROM
	(SELECT carrier, count(*) AS delays FROM ontime
	WHERE DepDelay > 15 GROUP BY carrier) t
	    
	JOIN
	(SELECT carrier, count(*) AS total_flights
	FROM ontime GROUP BY carrier) t2 ON (t.carrier=t2.carrier)
	
	LEFT JOIN (SELECT ccode, carrier FROM carriers) c ON t.carrier = c.ccode
	    
	WHERE delays > 10
    
	ORDER BY percent DESC

### Average Delay Duration

    SELECT ontime.carrier as ccode, c.carrier, avg(depdelay) as delay
    FROM ontime
    LEFT JOIN (SELECT carrier, ccode FROM carriers) c ON (ontime.carrier = c.ccode)
    GROUP BY 1, 2
    ORDER BY delay DESC

### Average Delay Duration (more than 15 minutes)

    SELECT ontime.carrier as ccode, c.carrier, avg(depdelay) as delay
    FROM ontime
    LEFT JOIN (SELECT carrier, ccode FROM carriers) c ON (ontime.carrier = c.ccode)
    WHERE depdelay > 15
    GROUP BY 1, 2
    ORDER BY delay DESC

## Dates

### Number of Flights per Days of Week

    SELECT FIRST 100 t.dayofweek,
           flights,
           flights * 100 / total_flights AS percent
    FROM
      (SELECT dayofweek, count(*) AS flights FROM ontime
       WHERE year > 2010 GROUP BY dayofweek) t
    
    JOIN
      (SELECT count(*) AS total_flights
       FROM ontime WHERE year > 2010) t2 ON '' = ''
    
    ORDER BY flights DESC

### Number of Flights per Days of Month

    SELECT FIRST 100 t.dayofmonth,
           flights,
           flights * 100 / total_flights AS percent
    FROM
      (SELECT dayofmonth, count(*) AS flights FROM ontime
       WHERE year > 2010 GROUP BY dayofmonth) t
    
    JOIN
      (SELECT count(*) AS total_flights
       FROM ontime WHERE year > 2010) t2 ON '' = ''
    
    ORDER BY flights DESC

### Most of Flights per Days of Year

    SELECT FIRST 100 t.month, t.dayofmonth,
           flights,
           flights * 100 / total_flights AS percent
    FROM
      (SELECT month, dayofmonth, count(*) AS flights FROM ontime
       WHERE year > 2010 GROUP BY month, dayofmonth) t
    
    JOIN
      (SELECT count(*) AS total_flights
       FROM ontime WHERE year > 2010) t2 ON '' = ''
    
    ORDER BY flights DESC

### Least of Flights per Days of Year

    SELECT FIRST 100 t.month, t.dayofmonth,
           flights,
           flights * 100 / total_flights AS percent
    FROM
      (SELECT month, dayofmonth, count(*) AS flights FROM ontime
       WHERE year > 2010 GROUP BY month, dayofmonth) t
    
    JOIN
      (SELECT count(*) AS total_flights
       FROM ontime WHERE year > 2010) t2 ON '' = ''
    
    ORDER BY flights

## Delays

### Delays per Year

	SELECT t.year, t.flights, t.flights * 100 / q_all.flights AS percent
	
	FROM (SELECT year, count(*) as flights FROM ontime WHERE DepDelay > 10 GROUP BY year) t
	
	JOIN
	  (SELECT year, count(*) AS flights
	   FROM ontime
	   GROUP BY YEAR) q_all
	
	ON t.year=q_all.year
	
	ORDER BY year DESC

### Average Delay Duration

	SELECT avg(depdelay) as delay
	FROM ontime
	WHERE year > 2010
	
### Average Delay Duration (more than 15 minutes)

	SELECT avg(depdelay) as delay
	FROM ontime
	WHERE year > 2010 AND depdelay > 15
	
### Average Delay by Year

	SELECT year, avg(depdelay) as delay
	FROM ontime
	GROUP BY year
	ORDER BY year DESC
	
### Average Delay by Year (more than 15 minutes)

	SELECT year, avg(depdelay) as delay
	FROM ontime
	WHERE depdelay > 15
	GROUP BY year
	ORDER BY year DESC

### Number of Delays per Days of Week

    SELECT FIRST 100 t.dayofweek,
           delays,
           delays * 100 / total_flights AS percent
    FROM
      (SELECT dayofweek, count(*) AS delays FROM ontime
       WHERE year > 2010 AND depdelay > 10 GROUP BY dayofweek) t
    
    JOIN
      (SELECT count(*) AS total_flights
       FROM ontime WHERE year > 2010) t2 ON '' = ''
    
    ORDER BY delays DESC

### Number of Delays per Days of Month

    SELECT FIRST 100 t.dayofmonth,
           delays,
           delays * 100 / total_flights AS percent
    FROM
      (SELECT dayofmonth, count(*) AS delays FROM ontime
       WHERE year > 2010 AND depdelay > 10 GROUP BY dayofmonth) t
    
    JOIN
      (SELECT count(*) AS total_flights
       FROM ontime WHERE year > 2010) t2 ON '' = ''
    
    ORDER BY delays DESC

### Most of Delays per Days of Year

    SELECT FIRST 100 t.month, t.dayofmonth,
           delays,
           delays * 10000000 / total_flights AS ratio
    FROM
      (SELECT month, dayofmonth, count(*) AS delays FROM ontime
       WHERE year > 2010 AND depdelay > 10 GROUP BY month, dayofmonth) t
    
    JOIN
      (SELECT month, dayofmonth, count(*) AS total_flights
       FROM ontime WHERE year > 2010 GROUP BY month, dayofmonth) t2 ON t.month = t2.month AND t.dayofmonth = t2.dayofmonth
    
    ORDER BY ratio DESC

### Least of Delays per Days of Year

    SELECT FIRST 100 t.month, t.dayofmonth,
           delays,
           delays * 10000000 / total_flights AS ratio
    FROM
      (SELECT month, dayofmonth, count(*) AS delays FROM ontime
       WHERE year > 2010 AND depdelay > 10 GROUP BY month, dayofmonth) t
    
    JOIN
      (SELECT month, dayofmonth, count(*) AS total_flights
       FROM ontime WHERE year > 2010 GROUP BY month, dayofmonth) t2 ON t.month = t2.month AND t.dayofmonth = t2.dayofmonth
    
    ORDER BY ratio
    
## Cancellations

### Most of Cancellations per Days of Year

    SELECT FIRST 100 t.month, t.dayofmonth,
           cancellations,
           cancellations * 10000000 / total_flights AS ratio
    FROM
      (SELECT month, dayofmonth, count(*) AS cancellations FROM ontime
       WHERE year > 2010 AND cancelled = 0 GROUP BY month, dayofmonth) t
    
    JOIN
      (SELECT count(*) AS total_flights
       FROM ontime WHERE year > 2010) t2 ON '' = ''
    
    ORDER BY ratio DESC

### Least of Cancellations per Days of Year

    SELECT FIRST 100 t.month, t.dayofmonth,
           cancellations,
           cancellations * 10000000 / total_flights AS ratio
    FROM
      (SELECT month, dayofmonth, count(*) AS cancellations FROM ontime
       WHERE year > 2010 AND cancelled = 0 GROUP BY month, dayofmonth) t
    
    JOIN
      (SELECT count(*) AS total_flights
       FROM ontime WHERE year > 2010) t2 ON '' = ''
    
    ORDER BY ratio
