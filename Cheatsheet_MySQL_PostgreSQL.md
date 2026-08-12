# Cheatsheet MySQL / PostgreSQL

## MySQL

### Connexion et bases
| Commande | Effet |
|---|---|
| `mysql -u root -p` | se connecter (mot de passe demandé) |
| `mysql -u user -p -h 10.0.0.5 base` | se connecter à distance |
| `SHOW DATABASES;` | lister les bases |
| `CREATE DATABASE monapp CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;` | créer une base |
| `USE monapp;` | sélectionner une base |
| `SHOW TABLES;` | lister les tables |
| `DESCRIBE table;` | structure d'une table |
| `SHOW GRANTS FOR 'user'@'host';` | privilèges d'un utilisateur |

### Utilisateurs et privilèges
| Commande | Effet |
|---|---|
| `CREATE USER 'app'@'localhost' IDENTIFIED BY 'mdp';` | créer un utilisateur |
| `GRANT ALL PRIVILEGES ON monapp.* TO 'app'@'localhost';` | tous les droits sur une base |
| `GRANT SELECT, INSERT, UPDATE ON monapp.* TO 'app'@'%';` | droits limités |
| `GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' WITH GRANT OPTION;` | super utilisateur |
| `REVOKE SELECT ON monapp.* FROM 'app'@'%';` | retirer un privilège |
| `FLUSH PRIVILEGES;` | recharger les privilèges |
| `ALTER USER 'app'@'localhost' IDENTIFIED BY 'nouveaumdp';` | changer le mot de passe |
| `DROP USER 'app'@'localhost';` | supprimer un utilisateur |

### SQL courant
```sql
CREATE TABLE users (
  id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  nom VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE,
  age TINYINT UNSIGNED,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO users (nom, email) VALUES ('Dupont', 'j@exemple.fr');
SELECT * FROM users WHERE age > 18 ORDER BY nom LIMIT 10;
UPDATE users SET age = 30 WHERE id = 1;
DELETE FROM users WHERE id = 1;

CREATE INDEX idx_email ON users (email);
```

### Sauvegarde / restauration
| Commande | Effet |
|---|---|
| `mysqldump -u root -p monapp > monapp.sql` | sauvegarde d'une base |
| `mysqldump -u root -p --all-databases > all.sql` | sauvegarde complète |
| `mysqldump -u root -p monapp | gzip > monapp.sql.gz` | sauvegarde compressée |
| `mysql -u root -p monapp < monapp.sql` | restaurer |
| `mysql -u root -p -e "DROP DATABASE monapp; CREATE DATABASE monapp;"` | reset rapide |

### Fichiers et service
| Chemin | Rôle |
|---|---|
| `/etc/mysql/mysql.conf.d/mysqld.cnf` | configuration (Debian) |
| `/var/lib/mysql/` | données |
| `/var/log/mysql/error.log` | logs d'erreur |
| `systemctl restart mysql` / `mysqld` | redémarrer le service |

## PostgreSQL

### Connexion
| Commande | Effet |
|---|---|
| `psql -U postgres` | se connecter (utilisateur postgres) |
| `psql -h 10.0.0.5 -U app -d base` | se connecter à distance |
| `sudo -u postgres psql` | se connecter via le compte système |
| `\l` | lister les bases |
| `\c base` | se connecter à une base |
| `\dt` | lister les tables |
| `\d table` | structure d'une table |
| `\du` | lister les rôles |
| `\q` | quitter |

### Rôles et privilèges
| Commande | Effet |
|---|---|
| `CREATE USER app WITH PASSWORD 'mdp';` | créer un rôle |
| `CREATE DATABASE monapp OWNER app;` | créer une base |
| `GRANT ALL PRIVILEGES ON DATABASE monapp TO app;` | droits sur la base |
| `GRANT SELECT, INSERT ON table TO app;` | droits sur une table |
| `ALTER USER app WITH PASSWORD 'nouveaumdp';` | changer le mot de passe |
| `DROP USER app;` | supprimer un rôle |
| `psql -U postgres -c "\du"` | lister les rôles (une commande) |

### SQL courant
```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  nom VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE,
  age SMALLINT,
  created_at TIMESTAMPTZ DEFAULT now()
);

INSERT INTO users (nom, email) VALUES ('Dupont', 'j@exemple.fr');
SELECT * FROM users WHERE age > 18 ORDER BY nom LIMIT 10;
UPDATE users SET age = 30 WHERE id = 1;
DELETE FROM users WHERE id = 1;

CREATE INDEX idx_users_email ON users (email);
```

### Sauvegarde / restauration
| Commande | Effet |
|---|---|
| `pg_dump monapp > monapp.sql` | sauvegarde d'une base |
| `pg_dump -Fc monapp > monapp.dump` | format compressé |
| `pg_dumpall > all.sql` | sauvegarde complète (inclut les rôles) |
| `psql -d monapp < monapp.sql` | restaurer (format texte) |
| `pg_restore -d monapp monapp.dump` | restaurer (format compressé) |
| `pg_dump monapp \| gzip > monapp.sql.gz` | sauvegarde compressée |

### Fichiers et service
| Chemin | Rôle |
|---|---|
| `/etc/postgresql/<version>/main/postgresql.conf` | configuration |
| `/etc/postgresql/<version>/main/pg_hba.conf` | authentification client |
| `/var/lib/postgresql/<version>/main/` | données |
| `systemctl restart postgresql` | redémarrer le service |

## MySQL vs PostgreSQL (raccourci)
| Critère | MySQL | PostgreSQL |
|---|---|---|
| Licence | GPL | PostgreSQL (open source) |
| Extensibilité | limitée | très extensible (types, fonctions) |
| Transactions | InnoDB | oui (AVC standard) |
| Index partiel | non | oui |
| JSON | JSON (type) | JSONB (indexé) |
| Type d'usage | web classique | applications complexes, analytics |

## Dépannage
| Problème | Vérification |
|---|---|
| Refus de connexion | service démarré ? port 3306/5432 ? `pg_hba.conf` ? |
| Mot de passe oublié (MySQL) | `sudo mysqld_safe --skip-grant-tables` puis reset |
| Mot de passe oublié (PG) | `sudo -u postgres psql` puis `ALTER USER` |
| Base lente | index manquants, `EXPLAIN`, `ANALYZE`/`OPTIMIZE` |
| Statistiques (PG) | `VACUUM ANALYZE;` |
| Logs | MySQL : `/var/log/mysql/error.log` ; PG : `journalctl -u postgresql` |
