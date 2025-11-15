# Semester Project Topic  
**Analysis of Player Deaths in World of Warcraft Hardcore**

## **Dataset Description**  
The dataset, _WoW Hardcore Deathlog_, originates from the **World of Warcraft player community** that participates in the so-called **Hardcore mode** — a playstyle featuring the *permadeath* rule (once a character dies, it is permanently lost).  
The data contain **records of player deaths**, including:
- the **character level** at the time of death (`level`),  
- **race** (`race_id`) and **class** (`class_id`),  
- the **map name and coordinates** where the player died (`map_id`, `map_pos`),  
- the **guild and character name** (`guild`, `name`),  
- the **timestamp of death** (`date`),  
- and the **source of death** (`source_id`, typically an enemy identifier).  

The dataset size is approximately **45.9 MB** and contains **tens of thousands of records**.  
It was originally provided in a **Lua table format**, which can be easily converted into **JSON** or **CSV** for data analysis.

---

## **Research Questions**

1. **Which character classes die most often, and at what levels?**  
   → Analyze the distribution of deaths by _class_id_ and _level_.  

2. **Is there a relationship between character level and the likelihood of dying in a specific location (`map_id`)?**  
   → Spatial analysis of “danger zones” for different level ranges.  

3. **How does the death frequency change over time — during the day, week, or season?**  
   → Time series analysis based on the _date_ timestamps.  

4. **Do death rates vary between different guilds?**  
   → Potential indicator of differences in teamwork or player experience.  

5. **Which races and classes reach the highest average levels before death?**  
   → Compute the “average level before death” as a measure of difficulty for each race/class combination.  

6. **Can the likelihood of surviving beyond a certain level be predicted based on character attributes (class, race, guild, level)?**  
   → Suitable for a classification or regression model — e.g., _Logistic Regression_ or _Random Forest_.  

7. **Are there geographic clusters of deaths (`map_pos`) within specific maps?**  
   → Apply spatial visualization (e.g., heatmap) to identify “death hotspots.”  
