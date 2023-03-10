# us-ego optimization model

This is a least cost dispatch model, based on year 2016 data that provides hourly emissions of NOx, SO2, and CO2 from every EGU in the United States. Model output is suitable for use in a chemical transport model.


Warning: the input csv files are not included here. They are stored in our current Dropbox folder on Guillaume's server (https://www.dropbox.com/sh/edxgc11umqw4y3b/AABlT1BPtHBfTTVNdsR5dA5da?dl=0)

1. Run modify_inputs, which loads Alan's input data from ./good_model_inputs/ and adjusts the costs and capacities. This gives you a new generation and transmission file.

2. Run check_feasibility.ipynb on the new generation and transmission files if you want to check whether or not there is a time where demand exceeds generation capacity+import capacity.

3. Launch your terminal and create a directory called 'outputs'

4. Run ./launch.sh, which will run the optimization (sliced into 8 different runs)
    - To edit the limit for time and CPUs for the run, edit runopt.sh. This is what launches runopt.jl, your optimization script, and is launched by "sbatch runopt.sh t1 t2" where t1 is the first hour of the simulation and t2 is the last one
    - To edit the time slices for the run, edit launch.sh
    - Your run will take anywhere from 1-2 days, after which you can look at the output
    - Edit the input files and output file names for runopt.jl (based on the output from 1) 

6. Run combined.ipynb to merge and format the outputs

7. Validate your output against eGrid generation and emissions with postproc_opt.ipynb, which takes your combined outputs

8. If you want to compare your data to NEI 2016 data, use NEI_validation.ipynb for a monthly validation (data from ftp://newftp.epa.gov/DMDnLoad/emissions/daily/quarterly/2016/)

9. To get the data in a format readable by GEOS-Chem, with access to a slurm run sbatch opt_output_to_inventory.py. You can then run remove_nans.ipynb in order to remove any nans that appear in the .nc files to make sure they work with GEOS-Chem.

Additional notes:
1. mask_us_neigrid.pkl masks the US grid, and is used in the opt_output_to_inventory

# Data Sources
Sources for all raw data are listed below. The input files are modified, as many have taken tables and turned them into CSV files. 
1. Cost data: https://www.eia.gov/electricity/data/eia923/ EIA-923 with EIA-906/920 previous data 
2. Solar Renewable CF: https://www.epa.gov/airmarkets/power-sector-modeling-platform-v515 Table 4-28 
3. Wind Renewable CF: https://www.epa.gov/airmarkets/power-sector-modeling-platform-v515 Table 4-20
4. Load/Demand data: https://www.eia.gov/todayinenergy/detail.php?id=27212 EIA930_BALANCE_2016 form for both Jan-Jun and Jul-Dec, selecting download data, balance data for 2016
5. Capacity/Emissions factors: https://www.epa.gov/energy/emissions-generation-resource-integrated-database-egrid (historical data, 2016)
6. Transmission Data and hourly wind and solar profiles are from: https://www.epa.gov/airmarkets/power-sector-modeling-platform-v515 NEEDS v5.16/IPM v5.16 
