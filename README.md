# RCBldEng (RC Building Energy Model)

RCBldEng is a building energy simulation tool that employs a Resistor-Capacitor (RC) network approach to model thermal dynamics, developed by Pengyuan Shen from Shenzhen International Graduate School of Tsinghua University. The program supports RC based thermal network modeling to simulate building energy performance. Key features include:

## Key Features

*   **Multi-zone building simulation capability**
*   **Comprehensive heat transfer modeling** including solar gains, internal loads, and ventilation
*   **Support for various HVAC systems and equipment configurations** in a simplified way
*   **Thermal comfort analysis** using the PMV method
*   **Natural ventilation and infiltration modeling**
*   **Integration of renewable energy systems** (PV, wind, solar water heating)
*   **Detailed hourly and monthly energy performance analysis**

## System Requirements

*   **Windows operating system**
*   **Minimum 4GB RAM**
*   **100MB free disk space**
*   **Python dependencies** (included in the executable)

## Installation

1. Download `RCBldEng.exe`.
2. Create a parent directory for the program.
3. Place `RCBldEng.exe` in the parent directory.
4. Create the required subdirectories (see Directory Structure).

## Directory Structure

```
Parent_Directory/
├── RCBldEng.exe
├── Projects/
│ └── [Building_Name]/
│ └── [Run_Code]/
├── Climate/
│ └── TMYs (user specified name)/ # weather files
```

## Input Files

### Required Input Files

1. **Building Definition File (`*.sim`)**
    *   Format: `[ProjectName]_[Run_Code].sim` (the subfolder name under the `[Building]` directory should be the same as `[Run_Code]`)
    *   Example: `office_Shanghai_v1.sim`
    *   Valid characters: letters, numbers, underscores
    *   No spaces or special characters allowed
    *   Case sensitive
    *   Maximum length: 50 characters
2. **Weather File (`*.epw`)**
    *   Standard EnergyPlus weather file
    *   Can be placed in the `Climate/TMYs/` directory

## Running Simulations

1. Open Command Prompt.
2. Navigate to the `RCBldEng.exe` location.
3. Run using the following syntax:

    ```
    RCBldEng.exe [building_name] [first_day] -sim [run_code]
    ```

    **Example:**

    ```
    RCBldEng.exe office_Shanghai mon -sim test
    ```

## Command Line Arguments and Keyword Arguments

**Required Arguments:**

`RCBldEng.exe [building_name] [first_day] -sim [run_code]`

**Keyword Arguments:**

*   `if_pmv` (bool, default=`False`): Calculate thermal comfort using the PMV method.
*   `if_print` (bool, default=`True`): Display simulation progress and status messages.
*   `solver` (str, default=`'crank-nicholson'`): Numerical solver selection.
    *   Options: `'euler'`, `'crank-nicholson'`, `'runge-kutta'`
*   `infl_style` (str, default=`'real'`): Infiltration calculation method.
    *   Options:
        *   `'constant'`: Fixed air change rate
        *   `'real'`: Dynamic calculation based on temperature difference and wind
*   `DHW_style` (str, default=`'real'`): Domestic hot water profile.
    *   Options:
        *   `'real'`: Hourly profile based on occupancy
        *   `'avg'`: Average daily consumption
        *   `'occ'`: Scaled by occupancy schedule
*   `weather_mode` (str, default=`'default'`): Weather data processing.
    *   Options:
        *   `'default'`: Use TMY data as provided
        *   `'interpolated'`: Linear interpolation of missing data
        *   `'smoothed'`: Apply moving average smoothing

**Example with kwargs:**

`RCBldEng.exe office_commercial_shanghai_v1 mon -sim test01 solver=runge-kutta if_pmv=True infl_style=real`

## Output Files

The program generates three output files in the `Projects/[Building_Name]/[Run_Code]/` directory:

1. `[building_name]_[run_code]_hourly.csv`
    *   Hourly results including:
        *   Indoor/outdoor temperatures
        *   Energy demands
        *   System performance
        *   Thermal comfort metrics
2. `[building_name]_[run_code]_monthly.csv`
    *   Monthly aggregated results
    *   Energy consumption by end-use
    *   System efficiencies
3. `[building_name]_[run_code]_indoor_temperature.csv`
    *   Detailed zone temperature data
    *   Thermal comfort parameters

**Note:** Users can find several example projects under the `Projects` folder. Please feel free to try some simulations with them and understand the naming conventions and model structure of RCBldEng.

## Technical Reference

### Building Definition File Structure

The `.sim` file requires the following sections:

1. **Basics:**

    ```
    $Basics:
    Building Type: [1-Residential, 2-Office, 3-Other]
    Weather File: [filename.epw]
    Building Area: [m²]
    Envelope Heat Capacity Type: [1-11]
    Ground Type: [1-3]
    ```

2. **Zones:**

    ```
    $Zones:
    Zone Name: [name]
    Area: [m²]
    Height: [m]
    Occupancy: [m²/person]
    ...
    ```

3. **HVAC:**

    ```
    $HVAC:
    HVAC Name: [name]
    System Type: [1-37]
    Heating Capacity: [kW]
    Cooling Capacity: [kW]
    ...
    ```

### RC Network Model

RCBldEng uses a simplified thermal network approach:

*   **1st order:** 7R1C network
*   **2nd order:** 7R2C network

Key parameters:

*   **Cm:** Internal thermal mass capacitance
*   **Htr:** Heat transfer coefficients
*   **Am:** Effective mass area
*   **At:** Internal heat transfer coefficient

### Heat Balance Components

The tool calculates:

1. **Solar gains through:**
    *   Windows (direct, diffuse, reflected)
    *   Opaque surfaces
2. **Internal gains from:**
    *   Occupants
    *   Equipment
    *   Lighting
3. **Ventilation heat transfer:**
    *   Mechanical ventilation
    *   Natural ventilation
    *   Infiltration
4. **Thermal storage effects**

## Troubleshooting

### Common Issues and Solutions

1. **"File not found" error**
    *   Verify the building definition file location.
    *   Check if the weather file exists in the `TMYs` folder.
    *   Ensure the correct file naming convention.
2. **"Invalid parameter" error**
    *   Review the building definition file syntax.
    *   Check the command line argument format.
    *   Verify numerical input ranges.
3. **Simulation crashes**
    *   Check available RAM.
    *   Verify input file completeness.
    *   Ensure weather data coverage.
4. **Unrealistic results**
    *   Verify building geometry.
    *   Check system specifications.
    *   Review schedules and setpoints.
