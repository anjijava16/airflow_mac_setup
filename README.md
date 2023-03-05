# airflow_mac_setup

Airflow is written in python, so python needs to be installed in the environment, and python must be greater than 2.7, 3.x is recommended.
Follow these steps to install Apache Airflow on Mac OS.
1. Install Python3
Install Python3 and then check to make sure the python version is 3+
% brew install python3

% python3 --version
Python 3.8.10
2. Open a Terminal window and execute below command.
mkdir -p ~/install
3. Create a working directory here called airflow-tutorial by executing below command.
mkdir -p ~/install/airflow-tutorial
4. Run below command to install python virtual environment.
pip3 install virtualenv
Output:
Collecting virtualenv
	Downloading virtualenv-20.13.1-py2.py3-none-any.whl (8.6 MB)
		 |████████████████████████████████| 8.6 MB 2.2 MB/s
Requirement already satisfied: six<2,>=1.9.0 in /usr/local/lib/python3.8/site-packages (from virtualenv) (1.15.0)
Collecting filelock<4,>=3.2
	Downloading filelock-3.6.0-py3-none-any.whl (10.0 kB)
Collecting distlib<1,>=0.3.1
	Downloading distlib-0.3.4-py2.py3-none-any.whl (461 kB)
		 |████████████████████████████████| 461 kB 1.3 MB/s
Collecting platformdirs<3,>=2
	Downloading platformdirs-2.5.1-py3-none-any.whl (14 kB)
Installing collected packages: platformdirs, filelock, distlib, virtualenv
Successfully installed distlib-0.3.4 filelock-3.6.0 platformdirs-2.5.1 virtualenv-20.13.1
5. Create a virtual environment.
virtualenv -p python3 ~/install/airflow-tutorial/airflow_venv
Output:
created virtual environment CPython3.8.10.final.0-64 in 789ms
	creator CPython3Posix(dest=/Users/rangareddy.avula/install/airflow-tutorial/airflow_venv, clear=False, no_vcs_ignore=False, global=False)
	seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/Users/rangareddy.avula/Library/Application Support/virtualenv)
		added seed packages: pip==22.0.3, setuptools==60.6.0, wheel==0.37.1
	activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator
6. Activate the virtual environment.
source ~/install/airflow-tutorial/airflow_venv/bin/activate
Output:
Now your virtual environment is activated. Your console should now have (airflow_venv) before it.
(airflow_venv) $
7. Install Airflow in this virtual environment by using pip3 command.
pip3 install apache-airflow
or
pip3 install apache-airflow==2.2.4
or
pip3 install apache-airflow[all]==2.2.4
Note: In case if you face any issues with pip while executing the above command, we can upgrade pip itself by using the below command:
python3 -m pip install -U pip
Once the pip upgrade is successful, we can try installing apache-airflow once again.
8. Create configuration files and metadata storage directories.
mkdir -p ~/install/airflow-tutorial/airflow
9. Set environment variables by exporting the AIRFLOW_HOME directory.
export AIRFLOW_HOME=~/install/airflow-tutorial/airflow
By default, airflow uses ~/airflow as it's AIRFLOW_HOME directory. We can overwrite this by setting a different path. Airflow will initialize the airflow.cfg file here along with the logs folder. We'll store our dags and plugins in this directory.
Alternatively, we can set a permanent environment variable in your bash_profile.
10. Initialize the metadata using the following command.
By default, Airflow uses sqlite database and following command initializes the necessary tables.
cd ${AIRFLOW_HOME}
airflow db init
Output:
Modules imported successfully
Initialization done
Airflow Installation Structure:
airflow                 # the root directory.
├── airflow.cfg         # global configuration for Airflow
├── airflow.db    		# SQLite database used by Airflow internally to track the status of each DAG.
├── logs
│   └── scheduler
│       ├── 2022-03-09
│       └── latest -> /Users/rangareddy.avula/install/airflow-tutorial/airflow/logs/scheduler/2022-03-09
└── webserver_config.py
Here, airflow.cfg file contains the configuration properties for the airflow and various settings. The airflow.db is the database file. Also, there is a log file and webserver_config.py.
Note: To disable the built in dags we need to set load_examples = False in ${AIRFLOW_HOME}/airflow.cfg file.
11. Setup Admin User
In order to access Airflow admin, we have to create admin user using the below command.
airflow users create \
--username admin \
--password admin \
--firstname Ranga \
--lastname Reddy \
--role Admin \
--email admin@example.com
Output:
User "admin" created with role "Admin"
Run the following command to list the users:
airflow users list
Output:
id | username | email             | first_name | last_name | roles
===+==========+===================+============+===========+======
1  | admin    | admin@example.com | Ranga      | Reddy     | Admin
12. Starting the Airflow scheduler and webserver
The scheduler is the component that actually manages and runs the various jobs. To start the scheduler, we can execute the below command:
sudo airflow scheduler -D
or
airflow scheduler \
--pid ${AIRFLOW_HOME}/logs/airflow-scheduler.pid \
--stdout ${AIRFLOW_HOME}/logs/airflow-scheduler.out \
--stderr ${AIRFLOW_HOME}/logs/airflow-scheduler.out \
-l ${AIRFLOW_HOME}/logs/airflow-scheduler.log \
-D
Start a new terminal (Ctrl+T to open a new window under Mac) in the airflow-tutorial, activate the virtual environment, and start the webserver.
export AIRFLOW_HOME=~/install/airflow-tutorial/airflow
source ${AIRFLOW_HOME}/airflow_venv/bin/activate
sudo airflow webserver --port 8080 -D
or
airflow webserver \
--pid ${AIRFLOW_HOME}/logs/airflow-webserver.pid \
--stdout ${AIRFLOW_HOME}/logs/airflow-webserver.out \
--stderr ${AIRFLOW_HOME}/logs/airflow-webserver.out \
-l ${AIRFLOW_HOME}/logs/airflow-webserver.log \
-D
After the scheduler and webserver have been initialized, open any browser and go to http://localhost:8080/. Port 8080 should be the default port for Airflow.
After logging in using our airflow username and password, we should see the webserver UI of airflow.
No alt text provided for this image
If load_examples = True in ${AIRFLOW_HOME}/airflow.cfg then only you will see some of the prebuilt dags otherwise you will see empty dag list.
Stop the webserver
ps -ef | egrep 'airflow webserver' | grep -v grep | awk '{print $2}' | xargs kill -9
or
cat ${AIRFLOW_HOME}/logs/airflow-webserver.pid | xargs kill -15
Stop the scheduler
ps -ef | egrep 'airflow scheduler' | grep -v grep| awk '{print $2}' | xargs kill -9
or
cat ${AIRFLOW_HOME}/logs/airflow-scheduler.pid | xargs kill -15
Thanks for Reading this article. Please contact me for any kind of issues.
