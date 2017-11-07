# SQL-Join Learn Challenge // Week 6 Day 2

### What languages are spoken in the United States? (12) Brazil? (not Spanish...) Switzerland? (6)

SELECT
	c.name AS country,
	cl.language AS language
FROM
	country c JOIN
    countrylanguage cl ON c.code = cl.countrycode
WHERE
	name = 'United States'
ORDER BY
	country,
    language


### What are the cities of the US? (274) India? (341)

SELECT
	c.code,
    c.name As country,
    ct.name AS city
FROM
	country c JOIN
    city ct ON (c.code = ct.countrycode)
WHERE
	c.name = 'United States'
ORDER BY
    city,
    code


### What are the official languages of Switzerland? (4 languages)

SELECT
	c.name AS country,
    cl.language AS language
FROM
	country c JOIN
    countrylanguage cl ON(c.code = cl.countrycode)
WHERE
	c.name = 'Switzerland' and
    cl.isofficial = true
ORDER BY
	name,
    language


### Which country or countries speak the most languages? (12 languages)

SELECT
	c.name,
    COUNT(cl.language) AS language_counter
FROM
	country c JOIN countrylanguage cl ON (c.code = cl.countrycode)
GROUP BY
    c.name
ORDER BY
    language_counter
    desc


### Which country or countries have the most offficial languages? (4 languages)

SELECT
	c.name,
    COUNT(cl.language) AS language_counter
FROM
	country c JOIN countrylanguage cl ON (c.code = cl.countrycode)
WHERE
	cl.isofficial = true
GROUP BY
    c.name
ORDER BY
    language_counter
    desc


### Which languages are spoken in the ten largest (area) countries?

WITH largest_10 AS(SELECT name, surfacearea, code FROM country ORDER BY surfacearea desc LIMIT 10)
SELECT
	c.name AS country,
    c.surfacearea AS area,
    cl.language AS language
FROM
	countrylanguage cl JOIN largest_10 c ON (c.code = cl.countrycode)

ORDER BY surfacearea desc

## What languages are spoken in the 20 poorest (GNP/ capita) countries in the world? (94 with GNP > 0)

WITH twentyPoorest AS (SELECT code, gnp, capital, name FROM country WHERE gnp > 0 ORDER BY (gnp/capital) LIMIT 20)
SELECT
	c.gnp AS gnp,
    c.name AS country,
    cl.language as language
FROM
	countrylanguage cl JOIN twentyPoorest c ON (c.code = cl.countrycode)
ORDER BY
	gnp/capital

### Are there any countries without an official language?

WITH officiallist AS (SELECT c.name, cl.countrycode, cl.isofficial
                      FROM country c JOIN countrylanguage cl ON (c.code = cl.countrycode)
                      WHERE cl.isofficial = 'true')
SELECT
	c.name,
    cl.countrycode AS countrycode,
    cl.isofficial AS official
FROM
	country c LEFT OUTER JOIN officiallist cl ON (c.code = cl.countrycode)
WHERE
	c.code NOT IN(SELECT countrycode FROM officiallist )
