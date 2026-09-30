# An Economics Research Project: General Instructions

## Project

- This economics research project studies how workers' education credentials link to their subsequent career paths using Revelio Labs individual career histories (scraped from LinkedIn).
- The basic idea is as follows:
    - Suppose two juniors are hired in the same company as the same role in the same cohort.
    - One junior is from a privileged university, while the other has a worse education background.
    - The fact that they are hired in the same company means that the firm receives better private signals (that are unobservable) in the junior with worse education credentials.
    - If the above signaling and selection logic is true, then the new hire with worse education credentials is actually more productive. 
    - This is the core (and somehow counter-intuitive) economic theory this project wants to test.
- The project is primarily descriptive, so the purpose is not to establish strong and robust causal effects.

## Data environment

- Complete Revelio datasets are stored in Microsoft Fabric.
- Descriptions of the Delta tables in Fabric are stored as markdown files in folder `E:/Dropbox/E_Projects/DistanceTraveled/data/a_raw_data/A_Revelio/Descriptions`.
    - Table names, column names, labels and schema are specified in these description markdown files.
- Random samples of the Delta tables are stored as parquet files in folder `E:/Dropbox/E_Projects/DistanceTraveled/data/a_raw_data/A_Revelio`.
    - They do not represent the actual production data.
    - They are useful for AI agents to understand the data when they develop the codes.
- AI agents will write jupyter notebooks (that will be executed on Fabric) to process the complete Revelio datasets.
    - IMPORTANT: For these notebooks, AI agents must review them and try to minimize computation costs, e.g., selecting relevant rows first before conducting computation-intensive data transformation tasks; never running unnecessary column transformation tasks on the full sample.

## Programming environment

- All Python files in this project should be executed through the `Talent` conda virtual environment.
    - Block `pip install`, `conda install`, `mamba install`, `uv add`, and similar commands.
    - If some packages are missing, stop and report which packages are missing. I will update the environment with the `Env_Talent.yml` to have all dependencies installed.
- For Python scripts, AI agents need to follow formatting instructions listed in `E:/Dropbox/E_Projects/DistanceTraveled/resources/_Prompts/Python_Formatting_Instructions.md`; unless specific requirements are made in the task contents.
