# Covid-19 Data Exploration

This project leverages SQL to analyze and uncover insights from the global Covid-19 pandemic data. By exploring cases, deaths, and vaccination statistics, this project highlights trends and patterns to understand the pandemic's impact across regions and populations.

---

## 📌 Project Overview
This project aims to:
- Analyze Covid-19 data to understand infection, death, and vaccination trends.
- Use advanced SQL techniques to prepare data for visualization and further exploration.
- Deliver meaningful insights for better understanding and decision-making.

---

## 🛠️ Skills Demonstrated
- **SQL Joins**: Combining datasets for enhanced analysis.
- **Common Table Expressions (CTEs)**: Simplifying complex queries for reusability.
- **Window Functions**: Performing rolling calculations and partitioned operations.
- **Temporary Tables**: Storing intermediate results for additional analysis.
- **Aggregate Functions**: Deriving summary statistics like totals and averages.
- **Views**: Creating reusable datasets for visualization and analysis.
- **Data Type Conversions**: Ensuring compatibility and accuracy of calculations.

---

## 📂 Datasets
- **CovidDeaths**: Contains data on Covid-19 cases, deaths, and population across locations.
- **CovidVaccinations**: Tracks vaccination progress across regions.

---

## 🔍 Analysis and Key Queries

### 1. **Initial Data Exploration**
   - **Purpose**: Examine raw data and key columns for analysis.
   - **Query**:
     ```sql
     SELECT *
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL
     ORDER BY 3, 4;
     ```

### 2. **Mortality Rate Analysis**
   - **Purpose**: Calculate the likelihood of dying from Covid-19.
   - **Query**:
     ```sql
     SELECT Location, date, total_cases, total_deaths, 
            (total_deaths / total_cases) * 100 AS DeathPercentage
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL
     ORDER BY 1, 2;
     ```
   - **Output**: Highlights mortality rates across countries.

### 3. **Infection Rates**
   - **Purpose**: Measure the percentage of populations infected by Covid-19.
   - **Query**:
     ```sql
     SELECT Location, date, Population, total_cases, 
            (total_cases / population) * 100 AS PercentPopulationInfected
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL
     ORDER BY 1, 2;
     ```
   - **Output**: Identifies regions with high infection rates.

### 4. **Countries with Highest Infection Rates**
   - **Purpose**: Identify countries most affected by Covid-19 relative to their population.
   - **Query**:
     ```sql
     SELECT Location, Population, 
            MAX(total_cases) AS HighestInfectionCount,  
            MAX((total_cases / population)) * 100 AS PercentPopulationInfected
     FROM PortfolioProject..CovidDeaths
     GROUP BY Location, Population
     ORDER BY PercentPopulationInfected DESC;
     ```

### 5. **Countries with Highest Death Count**
   - **Purpose**: Identify countries with the highest death count per population.
   - **Query**:
     ```sql
     SELECT Location, MAX(CAST(Total_deaths AS INT)) AS TotalDeathCount
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL
     GROUP BY Location
     ORDER BY TotalDeathCount DESC;
     ```

### 6. **Continent-Level Insights**
   - **Purpose**: Compare death counts and infection rates by continent.
   - **Query**:
     ```sql
     SELECT continent, MAX(CAST(Total_deaths AS INT)) AS TotalDeathCount
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL
     GROUP BY continent
     ORDER BY TotalDeathCount DESC;
     ```

### 7. **Global Summary**
   - **Purpose**: Analyze worldwide Covid-19 statistics, including cases, deaths, and death percentages.
   - **Query**:
     ```sql
     SELECT SUM(new_cases) AS total_cases, 
            SUM(CAST(new_deaths AS INT)) AS total_deaths, 
            SUM(CAST(new_deaths AS INT)) / SUM(new_cases) * 100 AS DeathPercentage
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL;
     ```

### 8. **Vaccination Trends**
   - **Purpose**: Track vaccination progress globally.
   - **Query**:
     ```sql
     SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
            SUM(CONVERT(INT, vac.new_vaccinations)) 
            OVER (PARTITION BY dea.Location ORDER BY dea.Date) AS RollingPeopleVaccinated
     FROM PortfolioProject..CovidDeaths dea
     JOIN PortfolioProject..CovidVaccinations vac
          ON dea.location = vac.location AND dea.date = vac.date
     WHERE dea.continent IS NOT NULL
     ORDER BY 2, 3;
     ```

### 9. **CTE for Vaccination Analysis**
   - **Purpose**: Simplify vaccination trends with reusable CTEs.
   - **Query**:
     ```sql
     WITH PopvsVac (Continent, Location, Date, Population, New_Vaccinations, RollingPeopleVaccinated) AS (
         SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
                SUM(CONVERT(INT, vac.new_vaccinations)) 
                OVER (PARTITION BY dea.Location ORDER BY dea.Date) AS RollingPeopleVaccinated
         FROM PortfolioProject..CovidDeaths dea
         JOIN PortfolioProject..CovidVaccinations vac
              ON dea.location = vac.location AND dea.date = vac.date
         WHERE dea.continent IS NOT NULL
     )
     SELECT *, (RollingPeopleVaccinated / Population) * 100 AS PercentVaccinated
     FROM PopvsVac;
     ```

### 10. **Temporary Table for Vaccination Analysis**
   - **Purpose**: Store vaccination calculations in a temporary table.
   - **Query**:
     ```sql
     DROP TABLE IF EXISTS #PercentPopulationVaccinated;
     CREATE TABLE #PercentPopulationVaccinated (
         Continent NVARCHAR(255),
         Location NVARCHAR(255),
         Date DATETIME,
         Population NUMERIC,
         New_vaccinations NUMERIC,
         RollingPeopleVaccinated NUMERIC
     );

     INSERT INTO #PercentPopulationVaccinated
     SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
            SUM(CONVERT(INT, vac.new_vaccinations)) 
            OVER (PARTITION BY dea.Location ORDER BY dea.Date) AS RollingPeopleVaccinated
     FROM PortfolioProject..CovidDeaths dea
     JOIN PortfolioProject..CovidVaccinations vac
          ON dea.location = vac.location AND dea.date = vac.date;

     SELECT *, (RollingPeopleVaccinated / Population) * 100 AS PercentVaccinated
     FROM #PercentPopulationVaccinated;
     ```

### 11. **Monthly Trends**
   - **Purpose**: Analyze trends in new cases on a monthly basis.
   - **Query**:
     ```sql
     SELECT continent, location, YEAR(date) AS Year, MONTH(date) AS Month,
            SUM(new_cases) AS MonthlyNewCases
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL
     GROUP BY continent, location, YEAR(date), MONTH(date)
     ORDER BY Year, Month, continent, location;
     ```
   - **Output**:
     - Provides insights into when cases peaked globally and regionally.
     - Aids in evaluating the effectiveness of interventions over time.

---

## 📖 How to Run
1. Load `CovidDeaths` and `CovidVaccinations` datasets into your SQL environment.
2. Execute the queries in order for a comprehensive analysis.
3. Use the final views and tables for visualization in Tableau or Power BI.

---

## 🤝 Connect
- **Email**: [syedmutaib0599@gmail.com](mailto:syedmutaib0599@gmail.com)
- **LinkedIn**: [Syed Mutaib Ali](https://linkedin.com/in/syedmutaibali)

---

This README ensures consistency and clarity while presenting your project on GitHub!
