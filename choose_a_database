#!/bin/bash

function stop_docker {
  docker rm -f $1_front &> /dev/null
  docker rm -f $1_api &> /dev/null
  docker rm -f $1_db &> /dev/null
}

function start_docker {
  docker-compose build db &> /dev/null
  docker-compose up -d db
  docker-compose build api &> /dev/null
  docker-compose up -d api
  docker-compose build front &> /dev/null
  docker-compose up -d front
}

function change_db_api {
  rm db_scripts/*ddl.sql &> /dev/null
  cp dbmovie/$1 db_scripts/
  previous_jdbc=$(head -1 movieapi/application.properties | cut -c2-)
  sed -i "s/$previous_jdbc/$2/g" movieapi/application.properties
}

function change_docker {
  rm docker-compose.yml
  cp conf/docker-$1.yml docker-compose.yml
}

echo 'Il y a 3 alternatives pour votre base de données'
echo 'Sélection        Alternative'
echo '-----------------------------------------------'

if [[ "$( docker container inspect -f '{{.State.Running}}' mariadb_db )" == "true" ]] &> /dev/null
then
  echo ' 1    [*]        MariaDB'
  running_database="mariadb"
else
  echo ' 1    [ ]        MariaDB'
fi
if [[ "$( docker container inspect -f '{{.State.Running}}' postgres_db )" == "true" ]] &> /dev/null
then
  echo ' 2    [*]        PosgreSQL'
  running_database="postgres"
else
  echo ' 2    [ ]        PosgreSQL'
fi
if [[ "$( docker container inspect -f '{{.State.Running}}' mysql_db )" == "true" ]] &> /dev/null
then
  echo ' 3    [*]        MySQL'
  running_database="mysql"
else
  echo ' 3    [ ]        MySQL'
fi

echo 'Appuyez sur Entrée pour conserver la valeur par défaut [*]'
echo 'Appuyez sur une autre touche pour supprimer les conteneurs'
read -p 'ou choisissez le numéro sélectionné pour changer de db : ' database

case "$database" in
	1) echo -e "\nAlternative MariaDB sélectionnée"
     database="mariadb"
     stop_docker "$running_database"
     change_db_api "my_cine_ddl.sql" "$database"
     change_docker "$database"
     start_docker ;;
	2) echo -e "\nAlternative PostgreSQL sélectionnée"
     database="postgres"
     stop_docker "$running_database"
     change_db_api "pg_cine_ddl.sql" "postgresql"
     change_docker "$database"
     start_docker ;;
	3) echo -e "\nAlternative MySQL sélectionnée"
     database="mysql"
     stop_docker "$running_database"
     change_db_api "my_cine_ddl.sql" "$database"
     change_docker "$database"
     start_docker ;;
 '') echo -e "\nAdieu !"
     exit -1;;
  *) echo -e "\nAlternative inconnue : $database"
     echo -ne "Je supprime les conteneurs ... "
     stop_docker "$running_database"
     echo "passez une bonne journée !"
	   exit -1;;
esac
