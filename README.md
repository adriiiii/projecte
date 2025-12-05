# Projecte Final d’Integració – Docker, Apache, PHP, MySQL i Redis

## 1. Arquitectura del Sistema

Aquest projecte implementa una arquitectura completa amb contenidors Docker interconnectats:

* **Frontend:** Apache + PHP, serveix la web i interactua amb l’API i Redis.
* **API Backend:** PHP gestionant peticions JSON i comunicant-se amb MySQL i Redis.
* **Base de dades:** MySQL 8.0.35 amb volums persistents.
* **Cache:** Redis 7-alpine per comptadors i dades temporals.
* **Administració:** phpMyAdmin per gestionar MySQL via interfície web.

Tots els serveis es comuniquen a través d’una xarxa Docker interna (`app_net`) i els volums permeten la persistència de dades.

---

## 2. Contingut del Dockerfile

El Dockerfile personalitzat inclou:

* Instal·lació de PHP 8.2 amb extensions `mysqli`, `pdo_mysql` i `redis`.
* Activació dels mòduls d’Apache necessaris: `ssl`, `rewrite`, `headers`.
* Copia de certificats SSL auto-signats per habilitar HTTPS.
* Configuració dels Virtual Hosts per a `frontend.local` i `api.local`.

---

## 3. Configuració de Xarxa i Seguretat

* **Virtual Hosts:**

  * `frontend.local` → Pàgina web principal.
  * `api.local` → API REST en format JSON.

* **HTTPS i redirecció:** Tot el tràfic HTTP (port 80) es redirigeix automàticament a HTTPS (port 443).

* **Capçaleres de seguretat:** HSTS, X-Frame-Options i CSP configurades per protegir el servidor.

* **Reescriptura d’URL:**

  * Frontend: URLs amigables sense extensió `.html`.
  * API: totes les rutes passen a `index.php` per permetre el router de l’API.

---

## 4. Persistència de Dades

* **MySQL:** Dades d’usuaris i articles en el volum `db_data`.
* **Redis:** Comptadors i dades temporals en el volum `redis_data`.

Això assegura que, encara que els contenidors es recreïn, les dades importants no es perdin.

---

## 5. Instruccions de Desplegament

### Configuració del fitxer hosts

Afegir al fitxer `hosts` del sistema:

```
127.0.0.1 frontend.local
127.0.0.1 api.local
```

### Inici del projecte

```bash
docker compose up --build -d
docker compose ps
```

**Estat dels contenidors:**
<img width="1466" height="276" alt="image" src="https://github.com/user-attachments/assets/4db7fd85-9038-4676-aae4-f2f4ac639f6d" />

### Accés als serveis

| Servei     | URL                                                      |
| ---------- | -------------------------------------------------------- |
| Web        | [https://frontend.local](https://frontend.local)         |
| API        | [https://api.local/articles](https://api.local/articles) |
| phpMyAdmin | [http://localhost:8080](http://localhost:8080)           |

---

## 6. Funcionament

### 6.1 Frontend (Web + Redis)

* La pàgina principal mostra els últims articles des de MySQL.
* Comptador de visites en temps real implementat amb Redis.

**Pàgina principal frontend:**
<img width="1192" height="827" alt="image" src="https://github.com/user-attachments/assets/d430e89f-a6f0-4e75-9d8c-229700b7ddd2" />


### 6.2 Creació d’articles

* Formulari al frontend per publicar nous articles via l’API.
* Els articles apareixen immediatament a la llista després de publicar.

**Creació d’un article:**
<img width="1068" height="506" alt="image" src="https://github.com/user-attachments/assets/a65857df-d542-4428-a143-8485a51e5c16" />

### 6.3 Articles publicats

* Últims articles mostrats en targetes amb títol, contingut i data de publicació.

**Articles a la web:**
<img width="1079" height="853" alt="image" src="https://github.com/user-attachments/assets/015ab157-f355-4167-89b9-936bf4e41112" />

### 6.4 Redirecció HTTPS

* Tot el tràfic HTTP és redirigit automàticament a HTTPS.

**Redirecció HTTPS:**
<img width="746" height="153" alt="image" src="https://github.com/user-attachments/assets/7005906f-37ab-46cd-b6f4-54e3d9e0ec59" />

### 6.5 Administració de la Base de Dades

* phpMyAdmin permet visualitzar i gestionar les taules `users` i `articles`.

**phpMyAdmin:**
<img width="888" height="810" alt="image" src="https://github.com/user-attachments/assets/a91749b1-89d6-4cec-aadd-eec96cbde506" />

### 6.6 API REST (JSON)

* L’API respon correctament a `/articles` i `/stats` amb format JSON.

**Resposta JSON de l’API:**
<img width="1917" height="258" alt="image" src="https://github.com/user-attachments/assets/ea87322a-316a-46d4-a2a4-95c86a9d120c" />

### 6.7 Logs d’Apache

* Logs separats per frontend i API disponibles al directori `logs/`.
* Permeten monitoritzar les peticions i errors.

**Logs d’Apache:**
<img width="1917" height="494" alt="image" src="https://github.com/user-attachments/assets/824153a7-8b03-4b53-80a1-5add1418d3ea" />

---

## 7. Credencials d’Accés

Les credencials es defineixen a `.env`:

```
DB_ROOT_PASSWORD=rootpassword
DB_DATABASE=my_app_db
DB_USER=user
DB_PASSWORD=password
```

* **phpMyAdmin:** usuari `user`, contrasenya `password`, host `mysql`.
