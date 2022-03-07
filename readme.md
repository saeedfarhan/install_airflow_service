# Update and Upgrade the system
```
sudo apt update -y && sudo apt upgrade -y
```
# Install the dependencies
```
sudo apt-get install mysql-server libpq-dev libmysqlclient-dev python3-pip sqlite3 -y
```
# Export and execute the airflow home directory
```
echo "export AIRFLOW_HOME=/opt/<your_proj_name>" | sudo tee /etc/profile.d/airflow.sh
```
# Install the requirements if you have
```
sudo pip3 install <requirements_here>
```
# Make directory for your project and change the permissions
```
sudo mkdir /opt/<project_name>
```
```
sudo chmod 777 /opt/<project_name>
```
# Initialize the airflow database
```
airflow db init
```
> By default airflow will use sqlite database :thinking:  
> You can change the database and run the command to upgrade
```
airlfow db upgrade
```
# Make sysconfig directory and paste the code in it
```
sudo mkdir /etc/sysconfig
```
```

#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

# This file is the environment file for Airflow. Put this file in /etc/sysconfig/airflow per default
# configuration of the systemd unit files.
#
AIRFLOW_CONFIG=/opt/cloudtdms/airflow.cfg
AIRFLOW_HOME=/opt/<project_name>
```

# Paste the airflow scheduler service code to  
> /usr/lib/systemd/system/airflow-scheduler.service
### Change the user and group
```
#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

[Unit]
Description=Airflow scheduler daemon
After=network.target postgresql.service mysql.service redis.service rabbitmq-server.service
Wants=postgresql.service mysql.service redis.service rabbitmq-server.service

[Service]
EnvironmentFile=/etc/sysconfig/airflow
User=<user>
Group=<group>
Type=simple
ExecStart=/usr/local/bin/airflow scheduler
Restart=always
RestartSec=5s

[Install]
WantedBy=multi-user.target
```
# Paste the airflow webserver service code to  
> /usr/lib/systemd/system/airflow-webserver.service
### Change the user and group
```
#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

[Unit]
Description=Airflow webserver daemon
After=network.target postgresql.service mysql.service redis.service rabbitmq-server.service
Wants=postgresql.service mysql.service redis.service rabbitmq-server.service

[Service]
EnvironmentFile=/etc/sysconfig/airflow
User=<user>
Group=<group>
Type=simple
ExecStart=/usr/local/bin/airflow webserver --pid /run/cloudtdms/webserver.pid
Restart=on-failure
RestartSec=5s
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```
# Copy the airflow conf service code to  
> /usr/lib/tmpfiles.d/airflow.conf
### Change the project name, user and group
```
#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

D /run/<project_name> 0755 <user> <group>
```

# Create directory to store airflow webserver PID
```
sudo mkdir /run/<project_name>
```
# Paste your project inside
> /opt/<project_name>/dags

# Change the permissions with your user and group
```
sudo chown -R <user>:<group> /run/<project_name>
```
```
sudo chmod -R 0755 /run/<project_name>
```
```
sudo chown -R <user>:<group> /opt
```

```
sudo chmod -R 775 /opt
```

# Create a user for airflow
```
airflow users create --username <user> --firstname <firstname> --lastname <lastname> --role Admin --email <email> --password <password>
```

# Start the airflow scheduler and webserver
```
sudo systemctl enable airflow-scheduler.service
```
```
sudo systemctl enable airflow-webserver.service 
```
```
sudo service airflow-webserver start
```
```
sudo service airflow-scheduler start
```



