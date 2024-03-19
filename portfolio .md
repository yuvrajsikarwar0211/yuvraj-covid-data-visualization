# yuvraj-covid-data-visualization
# [project:1 covid data dashboard] (https://public.tableau.com/app/profile/yuvraj.sikarwar/viz/coviddashboardproject_16930767390480/Dashboard1)

This is the dashboard i've created after exploring data form  covid dataset and after exploring i bring out the main topics  i find important using SQl and create a dashboard using tableau
* Data sets were taken from Our World in Data
* Data exploration using SQL
* Visualisation using tableau

* SQL code :-
 
select* 
from portfolio_project..covid_death 
where continent is not null
order by 1,2

select* 
from portfolio_project..covid_vaccination
where continent is not null
order by 1,2

Select location,date,total_cases,new_cases, total_deaths, population
from portfolio_project.. covid_death
order by 1,2


--looking at total casas vs totaldeaths
--shows likelihood of dying if you contract covid in your country

Select location,date,total_cases,new_cases, total_deaths, ( total_deaths ) * 100.0 / (total_cases) AS deathpercentage
from portfolio_project.. covid_death
where location like '%Afghanistan%'
order by 1,2

--looking at total population vs total cases
--shows what percentage of population got covid

Select location,date,total_cases, population, ( total_cases ) * 100.0 / ( population ) AS infectionpercentage
from portfolio_project.. covid_death
where location like '%Afghanistan%'
order by 1,2  

--country with highest infection rate

Select location, population,max(total_cases) as highestinfection_count, max( total_cases ) * 100.0 / ( population ) AS infectionpercentage
from portfolio_project.. covid_death
group by location, population 
--where location like '%Afghanistan%'
order by infectionpercentage desc

Select location, population ,date,max(total_cases) as highestinfection_count, max( total_cases ) * 100.0 / ( population ) AS infectionpercentage
from portfolio_project.. covid_death
group by location, population,date
--where location like '%Afghanistan%'
order by infectionpercentage desc

--totaldeath
Select location,max(total_deaths) as totaldeath_count
from portfolio_project.. covid_death
where continent is not null
group by location
order by totaldeath_count desc

select location, sum(convert(int,new_deaths)) as totaldeathcount
from portfolio_project..covid_death
where continent is null
and location not in ('world','european union','international')
group by location
order by totaldeathcount desc

--total death by continent
Select location,max(total_deaths) as totaldeath_count
from portfolio_project.. covid_death
where continent is null
group by location
order by totaldeath_count desc

--total worlds death percentage

Select sum(new_cases) as totalcases ,sum(new_deaths) as totaldeath_count ,sum(new_deaths )*100 /sum(new_cases ) as death_percentage
from portfolio_project.. covid_death
where continent is not null
--group by location
order by 1,2

--total population vs total vaccination

select dea.continent,dea.location,dea.date, dea.population, vac.new_vaccinations,
sum(convert(int, vac.new_vaccinations))over(partition by dea.location order by dea.location ,dea.date) as people_vaccination
from portfolio_project..covid_death dea
join portfolio_project..covid_vaccination vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
order by 2,3 

--use	CTE

WITH POPVSVAC(continent, location, date, population, people_vaccination, new_vaccionation)
as
(
select dea.continent,dea.location,dea.date, dea.population, vac.new_vaccinations,
sum(convert(bigint, vac.new_vaccinations))over(partition by dea.location order by dea.location ,dea.date) as people_vaccination
from portfolio_project..covid_death dea
join portfolio_project..covid_vaccination vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
--order by 2,3 
)
select* ,(people_vaccination/population)*100
from POPVSVAC

create view 
percentage_population_vaccinated 
as
select dea.continent,dea.location,dea.date, dea.population, vac.new_vaccinations,
sum(convert(bigint, vac.new_vaccinations))over(partition by dea.location order by dea.location ,dea.date) as people_vaccination
from portfolio_project..covid_death dea
join portfolio_project..covid_vaccination vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
--order by 2,3 

