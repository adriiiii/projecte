# Projecte Final d'Integració – Docker, Apache, MySQL, Redis i API REST

Aquest projecte consisteix en un entorn complet de desenvolupament utilitzant Docker Compose. Inclou un servidor Apache amb PHP, una API REST, una base de dades MySQL, un sistema de cache Redis i l'eina phpMyAdmin per gestionar la base de dades. L'objectiu és demostrar la integració i desplegament d'un stack complet amb diferents serveis que interactuen entre si.

---

# Estructura del Projecte

```
projecte-final/
├── docker-compose.yml       # Fitxer de definició dels serveis Docker
├── .env                     # Variables d'entorn per a la configuració
├── README.md                # Documentació del projecte
├── apache/
│   ├── Dockerfile           # Dockerfile per configurar Apache i PHP
│   ├── conf/
│   │   ├── httpd.conf       # Configuració global d'Apache
│   │   └── vhosts/          # Configuració dels Virtual Hosts
│   │       ├── frontend.conf
│   │       └── api.conf
│   ├── certs/               # Certificats SSL auto-signats
│   ├── sites/               # Contingut de les pàgines web i API
│   │   ├── frontend/
│   │   └── api/
├── mysql/
│   └── init/                # Script d'inicialització de la base de dades
│       └── 01-schema.sql
├── logs/                    # Logs d'Apache
└── ...
```

---

# Arquitectura del Sistema

El projecte segueix una arquitectura modular amb serveis independents que es comuniquen entre si:

* **Frontend (Apache + PHP):** Serveix la pàgina web principal i gestiona les sol·licituds a l'API.
* **API REST (PHP):** Rep les sol·licituds del frontend per crear i consultar articles.
* **MySQL:** Emmagatzema les dades de l'aplicació, com els usuaris i articles.
* **Redis:** S'utilitza com a cache per comptar visites i millorar el rendiment.
* **phpMyAdmin:** Eina web per gestionar la base de dades MySQL de manera visual.

L'estructura permet que cada servei funcioni de manera aïllada dins del seu contenidor, facilitant el desplegament i la gestió.

---

# Instruccions per Desplegar l'Aplicació

## Requisits previs
* Docker Compose ≥ 2.x

Afegir les entrades següents al fitxer hosts per poder accedir als dominis locals:

```
127.0.0.1 frontend.local
127.0.0.1 api.local
```

## Llançament del projecte

Per arrencar tots els serveis, executar la següent comanda dins del directori del projecte:

```
docker compose up --build -d
```

Per comprovar que els contenidors estan en execució:

```
docker compose ps
```

---

# URLs d’Accés

| Servei     | URL                                              | Notes                                 |
| ---------- | ------------------------------------------------ | ------------------------------------- |
| Frontend   | [https://frontend.local](https://frontend.local) | Acceptar certificat autosignat        |
| API REST   | [https://api.local](https://api.local)           | Permet consultar i crear articles     |
| phpMyAdmin | [http://localhost:8080](http://localhost:8080)   | Permet administrar MySQL              |

---

# Credencials

**Base de dades (definides a `.env`)**

```
DB_ROOT_PASSWORD=rootpassword
DB_DATABASE=my_app_db
DB_USER=user
DB_PASSWORD=password
```

**phpMyAdmin**

* Usuari: user
* Contrasenya: password
* Host: mysql

---

# Descripció dels Serveis

## Apache + PHP (Frontend)

Aquest servei serveix la pàgina web principal i està configurat amb dos Virtual Hosts: `frontend.local` i `api.local`. S'ha habilitat HTTPS amb un certificat auto-signat, així com els mòduls necessaris d'Apache com `ssl`, `rewrite` i `headers`. Els fitxers de la web i les rutes de l'API es troben dins del directori `sites/`.

Els logs d'Apache es dirigeixen al directori `logs/` per facilitar el seguiment de les peticions.

## API REST (PHP)

L'API permet interactuar amb la base de dades MySQL i opcionalment amb Redis per a estadístiques. Les rutes principals implementades són:

* `GET /articles` per obtenir la llista d'articles
* `POST /articles` per crear un nou article
* `GET /stats` per obtenir estadístiques d'articles i visites

Les respostes es retornen en format JSON.

## MySQL 8.0.35

Aquest servei conté la base de dades principal de l'aplicació. Es crea automàticament amb les taules `users` i `articles` mitjançant l'script `01-schema.sql`. Es mantenen les dades mitjançant un volum persistent.

## Redis

S'utilitza com a cache per comptar visites i millorar el rendiment del frontend. Les dades també es persisteixen en un volum per assegurar la continuïtat entre reinicis.

---

# Funcionalitats Implementades

* Comptador de visites en temps real amb Redis
* Llistat dels últims articles des de MySQL
* Formulari al frontend per crear nous articles via l'API sense recarregar la pàgina
* API REST amb rutes per consultar i crear articles
* Virtual Hosts amb HTTPS i capçaleres de seguretat
* Logging personalitzat d'Apache

---

# Captures Demanades

* **docker compose ps**
<img width="1466" height="276" alt="image" src="https://github.com/user-attachments/assets/3d7f662a-d818-4f04-b477-1cc9ac463cb6" />

* **Accés HTTPS a frontend.local**
<img width="917" height="703" alt="image" src="https://github.com/user-attachments/assets/aca8a480-3426-422d-8cbb-f1267e367ebf" />

* **phpMyAdmin amb taules**
<img width="888" height="810" alt="image" src="https://github.com/user-attachments/assets/767289ca-f061-47e8-8cee-ad216efb01fc" />

* **Output JSON de l’API**
  (Inserir captura aquí)

* **Logs d’Apache**
  (Inserir captura aquí)
