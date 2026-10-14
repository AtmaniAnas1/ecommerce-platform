<div align="center">

# Flask MongoDB E-Commerce

**Plateforme e-commerce full-stack : catalogue produits, panier, commandes et back-office d'administration.**

![Python](https://img.shields.io/badge/Python-3.8%2B-3776AB?logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-3.0-000000?logo=flask&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-NoSQL-47A248?logo=mongodb&logoColor=white)
![Jinja2](https://img.shields.io/badge/Jinja2-Templates-B41717?logo=jinja&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-blue)

</div>

---

## Présentation

**Flask MongoDB E-Commerce** est une application web de vente en ligne construite avec **Python / Flask** et une base de données **NoSQL MongoDB**. Elle couvre l'ensemble du parcours d'achat — de la consultation du catalogue jusqu'à la confirmation de commande — ainsi qu'un espace d'administration complet pour piloter la boutique.

Le modèle documentaire de MongoDB est exploité pour gérer des **produits à attributs variables** (taille, couleur, etc.) avec leurs propres prix et stocks, sans schéma rigide.

## Fonctionnalités

### Espace client
- **Catalogue** avec filtrage par catégorie et recherche plein texte
- **Fiche produit** avec images, variations (attributs, prix, stock) et produits similaires
- **Panier** en session : ajout, modification des quantités, suppression
- **Tunnel de commande** : adresse de livraison → paiement (carte / PayPal, simulé) → confirmation
- **Contrôle des stocks** au moment de la validation et décrément automatique
- **Espace compte** : modification du profil et historique des commandes

### Back-office administrateur
- **Tableau de bord** avec indicateurs clés (produits, commandes, clients, commandes en attente)
- **Gestion des produits** (CRUD) avec upload d'images et variations
- **Gestion des catégories** (CRUD, slugs uniques)
- **Suivi des commandes** avec filtrage et mise à jour du statut
- **Gestion des utilisateurs** : activation / désactivation des comptes

### Sécurité
- Hachage des mots de passe avec **bcrypt**
- Routes protégées par décorateurs `@login_required` et `@admin_required`
- Gestion des rôles (`client` / `admin`)
- Configuration sensible externalisée via variables d'environnement

## Stack technique

| Couche          | Technologies                                   |
|-----------------|------------------------------------------------|
| Backend         | Python 3, Flask 3 (Blueprints), Werkzeug       |
| Base de données | MongoDB, PyMongo                               |
| Frontend        | Jinja2, HTML5, CSS3, JavaScript                |
| Sécurité        | bcrypt, sessions Flask                         |
| Médias          | Pillow (traitement des images uploadées)       |
| Configuration   | python-dotenv                                  |

## Architecture

```
.
├── app.py                  # Point d'entrée Flask, enregistrement des blueprints
├── init_db.py              # Création des index et du compte administrateur
├── check_images.py         # Script de diagnostic des images produits
├── requirements.txt
├── env.example             # Modèle de configuration
│
├── src/
│   ├── models/             # Accès aux données MongoDB
│   │   ├── database.py     # Connexion (client singleton)
│   │   ├── product.py      # Produits & variations, recherche, stock
│   │   ├── category.py
│   │   ├── order.py        # Commandes, numérotation CMD-AAAAMMJJ-XXXXX
│   │   └── user.py
│   ├── routes/             # Blueprints Flask
│   │   ├── public.py       # Accueil, catalogue, produit, compte
│   │   ├── cart.py         # Panier, checkout, paiement, confirmation
│   │   ├── auth.py         # Connexion, inscription, déconnexion
│   │   └── admin.py        # Back-office
│   └── utils/
│       └── auth.py         # Hachage, décorateurs d'accès, utilisateur courant
│
├── templates/              # Vues Jinja2 (public, auth, admin, errors)
└── static/                 # CSS, JavaScript, images uploadées
```

### Collections MongoDB

| Collection   | Rôle                                         | Index principaux                          |
|--------------|----------------------------------------------|-------------------------------------------|
| `users`      | Comptes clients et administrateurs           | `email` (unique), `role`                  |
| `products`   | Produits, images et variations embarquées    | `categorie_id`, `publie`, texte (`nom`, `description`) |
| `categories` | Catégories du catalogue                      | `slug` (unique)                           |
| `orders`     | Commandes et articles                        | `numero_commande` (unique), `client_id`, `statut`, `date_commande` |

## Installation

### Prérequis
- Python **3.8+**
- **MongoDB Community Server** démarré localement (port `27017`) ou une instance MongoDB Atlas

### Étapes

```bash
# 1. Cloner le dépôt
git clone https://github.com/AnnasAttt/ecommerce-platform.git
cd ecommerce-platform

# 2. Créer et activer un environnement virtuel
python -m venv venv
venv\Scripts\activate        # Windows
source venv/bin/activate     # Linux / macOS

# 3. Installer les dépendances
pip install -r requirements.txt

# 4. Configurer l'environnement
cp env.example .env          # puis adapter les valeurs

# 5. Initialiser la base de données (index + compte admin)
python init_db.py

# 6. Lancer l'application
python app.py
```

L'application est accessible sur **http://localhost:5000** et le back-office sur **http://localhost:5000/admin**.

> **Astuce :** sous Windows, le script `start-mongodb.ps1` aide à démarrer MongoDB. Voir aussi [QUICKSTART.md](QUICKSTART.md), [INSTALL_MONGODB.md](INSTALL_MONGODB.md) et [MONGODB_TROUBLESHOOTING.md](MONGODB_TROUBLESHOOTING.md).

## Configuration

| Variable        | Description                         | Valeur par défaut            |
|-----------------|-------------------------------------|------------------------------|
| `MONGODB_URI`   | URI de connexion MongoDB            | `mongodb://localhost:27017/` |
| `DATABASE_NAME` | Nom de la base                      | `ecommerce_db`               |
| `SECRET_KEY`    | Clé de signature des sessions Flask | *à définir*                  |
| `FLASK_DEBUG`   | Mode debug                          | `True`                       |
| `APP_URL`       | URL publique de l'application       | `http://localhost:5000`      |

## Compte administrateur par défaut

Créé par `init_db.py` :

| Email               | Mot de passe |
|---------------------|--------------|
| `admin@example.com` | `admin123`   |

> **Important :** modifiez ce mot de passe et la `SECRET_KEY` avant tout déploiement.

## Pistes d'évolution

- Intégration d'une passerelle de paiement réelle (Stripe, PayPal SDK)
- Protection CSRF des formulaires (Flask-WTF)
- Tests automatisés (pytest) et intégration continue
- Conteneurisation Docker (application + MongoDB)
- API REST pour un client mobile ou SPA

## Auteur

**Anas Atmani** — [@AnnasAttt](https://github.com/AnnasAttt)

## Licence

Ce projet est distribué sous licence MIT.
