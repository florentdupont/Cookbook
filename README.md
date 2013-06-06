Cookbook JBoss CLI
==================

exemples de portions de code utiles pour les développements


Cookbook pour les commandes JBoss-CLI
-------------------------------------


pour faire des opérations sur la racine : ```/```
pour faire des opérations sur le noeud courant : ```./```

utiliser tab pour la completion


supprimer un noeud.
------------------
Par exemple supprimer le noeud datasource
```
 ./xa-data-source=organisme:remove()
```


Afficher tous les informations d'un noeud : 
-------------------------------------------
``` 
 :read-resource()
```


par exemple, pour lire tous les informations d'un datasource organisme:
----------------------------------------------------------------------
```
 /profile=ha/subsystem=datasources/xa-data-source=organisme:read-resource()
```

Pour lire la valeur de l'attribut "jndi-name"
---------------------------------------------
```
 /profile=ha/subsystem=datasources/xa-data-source=organisme:read-attribute(name=jndi-name)
```


Modifier une valeur d'un attribut : par exemple changer la valeur de l'attribut "jndi-name"
---------------------------------
```
 /profile=ha/subsystem=datasources/xa-data-source=organisme:write-attribute(name=jndi-name, value="test")
```


afficher les datasource properties, par exemple la propriété DatabaseName
-------------------------------------------------------------------------
```
 /profile=ha/subsystem=datasources/xa-data-source=organisme/xa-datasource-properties=DatabaseName:read-resource()
```

Attention : il n'est pas possible de modifier la valeur d'un xa-datasource-properties. Il faut le supprimer, puis le recréer.

l'autre solution est de se positionner sur le noeud avec cd
```
cd /profile=ha/subsystem=datasources/xa-data-source=organisme/xa-datasource-properties=DatabaseName
```
puis
```
 read-attribute --name value
 ```
ou 
```
 :read-resource()
```

ajouter une properties de datasource, par exemple, le port
-----------------------------------------------------------
```
 /profile=ha/subsystem=datasources/xa-data-source=organisme/xa-datasource-properties=Port:add(value="5432")
```

Supprimer la properties de datasource, par exemple, le port
-----------------------------------------------------------
```
 /profile=ha/subsystem=datasources/xa-data-source=organisme/xa-datasource-properties=Port:remove()
```


Ajouter un datasource XA en ligne de commande : 
-----------------------------------------------

Commande à effectuer à la racine ! (ajouter le --profile en mode domain)
```
xa-data-source add --name=organisme --jndi-name="java:jboss/datasources/organismeDS" \
--use-java-context=true \
--xa-datasource-class="org.postgresql.xa.PGXADataSource" --driver-name=postgresql --min-pool-size=0 \
--max-pool-size=10 \
--check-valid-connection-sql="SELECT current_timestamp"
```
puis ajouter les properties un par un :
```
/subsystem=datasources/xa-data-source=organisme/xa-datasource-properties=ServerName:add(value=ntels35)
/subsystem=datasources/xa-data-source=organisme/xa-datasource-properties=DatabaseName:add(value=db_int)
/subsystem=datasources/xa-data-source=organisme/xa-datasource-properties=User:add(value=organisme)
/subsystem=datasources/xa-data-source=organisme/xa-datasource-properties=Password:add(value=organisme) 
```

Tester la connecion dans le pool
--------------------------------
En JBoss EAP 6, ou AS 7.1.2. En domain, Il faut se positionner sur le server pour effectuer la commande test-connection-in-pool
Attention, l'ajout de datasource à chaud, nécessite un redémarrage du HC pour que la commande de tests soit effective.
cf : https://issues.jboss.org/browse/AS7-2199.
```
/host=myhost/server=myserver/subsystem=datasources/xa-data-sources=entrepriseDS:test-connection-in-pool
```
(retourne true normallement)

