# Gi Effektivt

## 🗃 Connecting to our database 

Our mysql database is hosted in google cloud. Databases in google cloud are only available through the internal google cloud network. Therefore it is nescesarry to use a [proxy](https://en.wikipedia.org/wiki/Proxy_server) to the internal network to gain access to our production database.

Make sure you have a google cloud account, and get someone in the team to authorize you with the SQL client privilege. 

> ℹ️ **You must be authorized before proceeding.**

> It's preferable that your google cloud account is the same as your EA Norway google account, if you have one (i.e. an account with a @effektivaltruisme.no email).

The next step is to download the google gloud sdk to authorize the client locally. The google cloud sdk is a comprehensive command line tool for interacting with google cloud services. For our purposes, we only need to initialize it to authorize a connection to the database. If prompted for a project when initializing, you may ignore this. It is *not* nescesarry to select a project. Follow googles [quickstart guide](https://cloud.google.com/sdk/docs/quickstart) to get up and running.

After you have succesfully initialized the sdk with the `gcloud init` command, you should be able to run `gcloud config list` to confirm that it's configured correctly. It should look something like this, but note that no project needs to be selected.

<img src="https://github.com/stiftelsen-effekt/.github/blob/main/profile/gcloud_config.png" width="500" />

### Google cloud sql auth proxy

When connecting to a google cloud sql instance, we are required to connect through [cloud sql auth proxy](https://cloud.google.com/sql/docs/mysql/connect-admin-proxy). Having authorized the google cloud cli, follow the instructions in the google documentation. The instance name of our database is `hidden-display-243419:europe-north1:effekt-db`. Thus, the command to setup the proxy is

<details open>
  <summary>MacOS / linux</summary>
  
  ```sh
 ./cloud-sql-proxy hidden-display-243419:europe-north1:effekt-db=tcp:3306
  ```
</details>
<details>
  <summary>Windows</summary>
  
  ```
  cloud_sql_proxy.exe -instances=hidden-display-243419:europe-north1:effekt-db=tcp:3306
  ```
</details>

if cloud_sql_proxy is located in the same folder in your terminal. We recommend you store the binary somewhere on your computer, and add it to your path.

> 💡 It's also possible to create an alias on macos / linux to avoid having to type in the entire command each time you wish to connect.

If all has gone well, you should be seeing this in your terminal of choice.

<img src="https://github.com/stiftelsen-effekt/.github/blob/main/profile/sql_proxy_terminal.png" width="500" />

The proxy is now listening for connections on port 3306 (the standard mysql port), and forwards any communication to the internal google cloud network through a secure tunnel.

### Connecting with a mysql client

The choice of mysql client for interacting with the database is up to you. The preferred choice has thus far been mysql workbench, and we will show how to connect with that particular client, but the steps should be similar for other alternatives (including the mysql command line client).

> ⚠️ MySql workbench has from version 8.0.27 removed the option in the dropdown menu to disable ssl on connections. We do not require SSL as we are connecting to localhost and then to the sql server via a secure tunnel. Either download version 8.0.26, or use the advanced tab to disable ssl as suggested [here](https://stackoverflow.com/questions/69747663/how-to-configure-mysql-workbench-to-not-require-ssl-encryption).

First, download mysql workbench [here](https://dev.mysql.com/downloads/workbench/). 

Once you have mysql workbench up and running, add a new connection. You may name it whatever you want. The host should be `localhost` or `127.0.0.1`. Ask on slack in the tech channel to recieve a username and password for the server on a private message. In the SSL tab, select `if available`, as we do not need SSL on our connection on localhost. After entering the parameters, you may press test connection to verify that everything works.

|<img src="https://github.com/stiftelsen-effekt/.github/blob/main/profile/workbench_parameters.png" />|<img src="https://github.com/stiftelsen-effekt/.github/blob/main/profile/workbench_ssl.png"/>|<img src="https://github.com/stiftelsen-effekt/.github/blob/main/profile/workbench_test.png" />|
|-|-|-|

Double clicking the connection on the home tab will connect you to the database.


<img src="https://github.com/stiftelsen-effekt/.github/blob/main/profile/workbench_connected.png" width="500" />

### Concluding remarks

Congratulations! You are now connected to our production databse. Note that there are two databases on our mysql instance, one for production and one for development. Feel free to experiment and change the schema of the development database, but avoid changing the schema of the production database, as it may break any running application. If you have any questions, please feel free to ask them in the tech slack channel.
