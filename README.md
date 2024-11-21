# Covid-19 Data Exploration

This project demonstrates how SQL can be utilized to analyze large datasets to uncover meaningful insights about the global Covid-19 pandemic. By analyzing Covid-19 cases, deaths, and vaccination data, this project offers valuable insights into infection rates, vaccination trends, and mortality patterns across the globe.

---

## 📌 Project Overview
This project focuses on exploring and analyzing Covid-19 data to:
- Understand the spread and impact of Covid-19 globally.
- Derive meaningful insights using SQL queries.
- Prepare data for visualization in tools like Tableau or Power BI.

---

## 🛠️ Skills Demonstrated
- **SQL Joins**: Combining data from multiple tables for richer analysis.
- **Common Table Expressions (CTEs)**: Simplifying complex queries for better readability and reusability.
- **Window Functions**: Performing calculations across partitions of data.
- **Temporary Tables**: Creating intermediate results for dynamic analysis.
- **Aggregate Functions**: Summarizing data to derive trends and insights.
- **Data Type Conversion**: Ensuring compatibility and accuracy of calculations.
- **Views**: Creating reusable datasets for visualization and advanced reporting.

---

## 📂 Datasets
1. **CovidDeaths.xlsx**: Contains data on Covid-19 cases, deaths, and population for various locations worldwide.
2. **CovidVaccinations.xlsx**: Tracks vaccination progress for each location.

---

## 🔍 Analysis and Key Queries
### 1. **Initial Data Exploration**
   - **Purpose**: Examine the raw data to identify useful columns.
   - **Key Metrics**: `total_cases`, `total_deaths`, `population`.
   - **Query**:
     ```sql
     SELECT *
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL
     ORDER BY 3, 4;
     ```

### 2. **Mortality Rate Analysis**
   - **Purpose**: Calculate the likelihood of dying from Covid-19 in different countries.
   - **Query**:
     ```sql
     SELECT Location, date, total_cases, total_deaths, 
            (total_deaths / total_cases) * 100 AS DeathPercentage
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL
     ORDER BY 1, 2;
     ```
   - **Output**:
     - Identifies regions with high mortality rates.
     - Helps in understanding the severity of the pandemic in different locations.

### 3. **Infection Rates**
   - **Purpose**: Measure the percentage of the population infected by Covid-19.
   - **Query**:
     ```sql
     SELECT Location, date, population, total_cases, 
            (total_cases / population) * 100 AS PercentPopulationInfected
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL
     ORDER BY 1, 2;
     ```
   - **Output**:
     - Highlights how widespread infections were relative to population size.
     - Useful for identifying the most affected regions.

### 4. **Vaccination Trends**
   - **Purpose**: Track vaccination progress over time.
   - **Query**:
     ```sql
     SELECT dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations,
            SUM(CONVERT(int, vac.new_vaccinations)) 
            OVER (PARTITION BY dea.Location ORDER BY dea.date) AS RollingPeopleVaccinated
     FROM PortfolioProject..CovidDeaths dea
     JOIN PortfolioProject..CovidVaccinations vac
          ON dea.location = vac.location AND dea.date = vac.date
     WHERE dea.continent IS NOT NULL
     ORDER BY 2, 3;
     ```
   - **Output**:
     - Displays cumulative vaccinations for each location.
     - Useful for comparing vaccination rollouts across countries.

### 5. **Continent-Level Insights**
   - **Purpose**: Compare death rates and infection trends by continent.
   - **Query**:
     ```sql
     SELECT continent, MAX(total_deaths) AS TotalDeathCount
     FROM PortfolioProject..CovidDeaths
     WHERE continent IS NOT NULL
     GROUP BY continent
     ORDER BY TotalDeathCount DESC;
     ```
   - **Output**:
     - Highlights the continents with the most severe outcomes.
     - Helps policymakers prioritize regions for support.

### 6. **Monthly Trends**
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

## 🚀 Project Features
1. **Global Summary**:
   - Analyze the global spread of Covid-19, total cases, and deaths.
2. **Mortality and Infection Trends**:
   - Identify the most and least affected countries.
3. **Vaccination Analysis**:
   - Track vaccination rollouts and highlight countries with the highest vaccination rates.
4. **Regional Insights**:
   - Compare continents based on death and infection rates.

---

## 📖 How to Run the Project
1. **Set Up the Environment**:
   - Load `CovidDeaths.xlsx` and `CovidVaccinations.xlsx` into a SQL database.
2. **Run Queries**:
   - Execute the SQL queries in a sequential order to replicate the analysis.
3. **Visualize**:
   - Use Tableau or Power BI to create visualizations from the output views.

---

## 🔮 Future Enhancements
- Integrate machine learning to predict future trends in cases and vaccinations.
- Build a real-time dashboard for live data updates.
- Analyze socio-economic factors influencing Covid-19 outcomes.

---

## 🤝 Connect
Feel free to reach out for questions or suggestions:
- **Email**: [syedmutaib0599@gmail.com](mailto:syedmutaib0599@gmail.com)
- **LinkedIn**: [Syed Mutaib Ali](https://linkedin.com/in/syedmutaibali)

---

