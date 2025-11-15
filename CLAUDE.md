# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a semester project analyzing player deaths in **World of Warcraft Classic Hardcore** mode. The project uses the WoW Hardcore Deathlog dataset (approximately 45.9 MB, ~96,603 death records) to explore patterns in player deaths, including class survivability, dangerous zones, level distributions, and death sources.

### Research Questions

1. Which character classes die most often, and at what levels?
2. Relationship between character level and death locations (`map_id`)
3. Death frequency over time (day/week/season patterns)
4. Guild-based death rate variations
5. Average levels reached before death by race/class combinations
6. Survival prediction models based on character attributes
7. Geographic clustering of deaths within maps (`map_pos` heatmaps)

## Dataset Structure

The primary data file is `deathlog.json` (or `deathlog-2.json` for larger dataset), originally from the [Deathlog project](https://github.com/aaronma37/Deathlog).

### Key Data Columns

- `map_id`: Zone identifier where death occurred
- `map_pos`: Coordinates `[x, y]` of death location
- `class_id`: Player class (1=Warrior, 2=Paladin, 3=Hunter, 4=Rogue, 5=Priest, 7=Shaman, 8=Mage, 9=Warlock, 11=Druid)
- `area_id`: More granular location identifier
- `source_id`: Cause of death (NPC/enemy identifier)
- `level`: Player level at time of death (1-60)
- `instance_id`: Dungeon/raid identifier (mostly NaN for open-world deaths)

### ID Mappings

**Important**: ID-to-name mappings (classes, maps, areas) are **not** in the dataset itself. They were manually extracted from the [Deathlog source code](https://github.com/aaronma37/Deathlog) and are hardcoded in the analysis notebook.

Map IDs range from 947 (Azeroth) to 1452 (Winterspring), covering all Classic WoW zones. The notebook contains complete mappings in `class_id_to_class_name` and `map_id_to_map_name` dictionaries.

## Development Environment

### Python Environment Setup

The project uses Python 3.14 with a virtual environment:

```bash
# Activate virtual environment
source .venv/bin/activate

# Deactivate when done
deactivate
```

### Required Dependencies

The project requires the following Python packages (installed in `.venv`):

- `pandas` (2.3.3) - Data manipulation
- `numpy` (2.3.4) - Numerical operations
- `matplotlib` (3.10.7) - Plotting
- `seaborn` (0.13.2) - Statistical visualizations
- `jupyterlab` (4.4.10) - Notebook interface

**Note**: There is no `requirements.txt` file. To recreate the environment, install the above packages manually or generate requirements from the venv:

```bash
pip install pandas numpy matplotlib seaborn jupyterlab
```

### Running the Analysis

```bash
# Start Jupyter Lab
jupyter lab

# Or run Jupyter Notebook
jupyter notebook
```

The main analysis is in `analysis.ipynb`.

## Analysis Architecture

### Data Pipeline

1. **Data Loading**: JSON data loaded with `json.load()` and converted to pandas DataFrame
2. **Type Conversion**: All numeric columns converted from `float64` to `Int64` (nullable integer type) to properly handle NaN values while maintaining integer semantics
3. **ID Mapping**: Hardcoded dictionaries map numeric IDs to human-readable names
4. **Analysis & Visualization**: Multiple EDA perspectives (level distribution, class analysis, map heatmaps)

### Key Preprocessing Steps

All numeric columns should be converted to `Int64` (not `int64`) to allow NaN values:

```python
int_columns = ['map_id', 'class_id', 'area_id', 'source_id', 'level', 'instance_id']
for col in int_columns:
    df[col] = df[col].astype('Int64')
```

### Analysis Sections in Notebook

1. **Data Structure Exploration**: Load JSON, inspect schema, display mappings
2. **Preprocessing**: Type conversions, missing value handling
3. **Level Distribution**: Histogram showing death counts across levels 1-60
4. **Class Analysis**:
   - Raw death counts per class
   - **Population-adjusted deaths** using AOTC leaderboard data to normalize by class popularity
5. **Map Analysis**: Top 10 deadliest zones, level-vs-map heatmap with logarithmic scaling
6. **Spatial Analysis** (planned): Using `map_pos` coordinates for heatmaps

### Population Normalization Methodology

To account for class popularity, deaths are normalized against total player counts from the [AOTC Classic Hardcore Leaderboard](https://hc.aotc.gg/population). The normalization formula:

```python
adjusted_deaths[class_id] = death_count / (class_population / total_population)
```

This reveals **per-capita** death rates, showing which classes are inherently more dangerous to play.

## Important Implementation Details

### Heatmap Visualization

The level-vs-map heatmap uses **logarithmic normalization** (`LogNorm()`) due to the wide range of death counts:

```python
sns.heatmap(data, norm=LogNorm(), cmap='viridis')
```

This prevents low-death zones from being invisible next to high-death zones.

### Coordinate Parsing

The `map_pos` field stores coordinates as lists `[x, y]`. To use for spatial analysis:

```python
df[['x', 'y']] = pd.DataFrame(df['map_pos'].tolist(), index=df.index)
```

### Missing Data Patterns

- `instance_id`: 95,791 missing (98.9%) - deaths are predominantly in the open world
- `map_id` / `map_pos`: 812 missing (0.8%) - likely data collection issues
- Other columns: complete (no missing values)

## Project Files

- `analysis.ipynb`: Main Jupyter notebook with all EDA and visualizations
- `deathlog.json`: Primary dataset (~13.9 MB, smaller subset)
- `deathlog-2.json`: Extended dataset (~45.9 MB, full data)
- `topic.md`: Project requirements and research questions
- `possible_EDA_charts.md`: Brainstorming notes for additional visualizations
- `.venv/`: Python virtual environment (not tracked in git)
- `.idea/`: IDE configuration files (PyCharm/IntelliJ)

## Common Development Workflows

### Adding New Visualizations

Follow the existing pattern in the notebook:

1. Create a markdown cell with `## Section Title`
2. Write analysis code in a code cell
3. Add a markdown cell with `### Insights` to interpret findings
4. Use `plt.figure(figsize=(10, 6))` for consistent sizing
5. Always add titles and axis labels to plots

### Working with ID Mappings

When adding new mappings (e.g., area_id or source_id), manually extract from the [Deathlog source code](https://github.com/aaronma37/Deathlog) and add as a dictionary in the notebook:

```python
new_id_to_name = {
    1: 'Name1',
    2: 'Name2',
    # ...
}
```

Then use `.rename()` or `.replace()` for mapping in visualizations.

### Handling Large Datasets

If working with `deathlog-2.json` causes memory issues:

- Filter to specific level ranges or maps before analysis
- Use sampling for exploratory work: `df.sample(n=10000)`
- Consider using `dtype='category'` for high-cardinality columns

## Data Source Attribution

Dataset: [Deathlog for WoW Hardcore](https://github.com/aaronma37/Deathlog)
Population data: [AOTC Classic Hardcore Leaderboard](https://hc.aotc.gg/population)
