# data-engineering-zoomcamp-2006-homework-02 

Question 1:
Within the execution for Yellow Taxi data for the year 2020 and month 12: what is the uncompressed file size (i.e. the output file yellow_tripdata_2020-12.csv of the extract task)? (1 point)
Ans: 134.5 MiB

Modify the extract task to check for file size

  - id: extract
    type: io.kestra.plugin.scripts.shell.Commands
    outputFiles:
      - "*.csv"
    taskRunner:
      type: io.kestra.plugin.core.runner.Process
    commands:
      - wget -qO- https://github.com/DataTalksClub/nyc-tlc-data/releases/download/{{inputs.taxi}}/{{render(vars.file)}}.gz | gunzip > {{render(vars.file)}}
      - echo "=== FILE SIZE CHECK ==="
      - ls -lh {{render(vars.file)}}
      - du -h {{render(vars.file)}}
      - du -m {{render(vars.file)}}
      - echo "======================="

Question 2:
What is the rendered value of the variable file when the inputs taxi is set to green, year is set to 2020, and month is set to 04 during execution? (1 point)
Ans: green_tripdata_2020-04.csv

data: "{{outputs.extract.outputFiles[inputs.taxi ~ '_tripdata_' ~ inputs.year ~ '-' ~ inputs.month ~ '.csv']}}"

Question 3:
How many rows are there for the Yellow Taxi data for all CSV files in the year 2020? (1 point)
Ans: 24,648,499

select count(*) from public.yellow_tripdata
WHERE filename LIKE 'yellow_tripdata_2020-%';


Question 4:
How many rows are there for the Green Taxi data for all CSV files in the year 2020? (1 point)
Ans: 1,734,051

SELECT COUNT(*) 
FROM public.green_tripdata 
WHERE filename LIKE 'green_tripdata_2020-%';


Question 5:
How many rows are there for the Yellow Taxi data for the March 2021 CSV file? (1 point)
Ans: 1,925,152

SELECT COUNT(*) 
FROM public.yellow_tripdata 
WHERE filename = 'yellow_tripdata_2021-03.csv';


Question 6:
How would you configure the timezone to New York in a Schedule trigger? (1 point)
Ans: Add a timezone property set to America/New_York in the Schedule trigger configuration

triggers:
  - id: green_schedule
    type: io.kestra.plugin.core.trigger.Schedule
    cron: "0 9 1 * *"
    timezone: America/New_York
    inputs:
      taxi: green
