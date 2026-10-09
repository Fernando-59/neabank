# NeaBank — déploiement sur Render

## Déploiement
1. Pousse le projet sur GitHub, sans inclure `.venv/`, `neobank.db` ni aucun fichier `.env`.
2. Sur Render, crée un Blueprint à partir du dépôt et applique `render.yaml`.
3. Renseigne `MAIL_USERNAME` et `MAIL_PASSWORD` dans Render avec les identifiants SMTP Gmail. Pour Gmail, utilise un mot de passe d'application.
4. Dans les variables d'environnement Render, définis `ADMIN_USERNAME` (adresse de connexion à `/admin`) et `ADMIN_PASSWORD` (un mot de passe fort d'au moins 12 caractères). Render génère `SECRET_KEY` et fournit `DATABASE_URL` depuis la base PostgreSQL déclarée dans le Blueprint.

## Configuration Gmail en local
1. Copie `.env.example` dans un fichier `.env` à la racine du projet.
2. Renseigne `MAIL_USERNAME` avec ton adresse Gmail et `MAIL_PASSWORD` avec un mot de passe d'application Google (pas le mot de passe normal de ton compte).
3. Redémarre Flask après avoir modifié `.env`. Ce fichier est ignoré par Git et ne doit jamais être envoyé sur GitHub.

## Inscription et activation de démonstration
- Depuis « Ouvrir un compte », la personne renseigne son nom, son prénom, son adresse e-mail et son téléphone, puis les autres informations nécessaires au compte de simulation.
- Après « Suivant », la demande et son code sont conservés. Si le client quitte puis revient dans les 24 heures, il ressaisit les mêmes informations et reprend le même code ; un nouveau code n'est généré qu'après expiration. Dans `/admin`, ouvre « Codes d’inscription » pour communiquer le code affiché.
- Le code n'est consommé qu'une seule fois, lors de sa validation réussie. Il expire après 24 heures et la demande est verrouillée après 5 codes incorrects. Seul un code valide crée le compte et donne accès à `/activation`.
- Le nom, le prénom, l'e-mail et le téléphone sont repris depuis les informations d'inscription.
- Le formulaire est réservé à la session connectée et enregistre les dossiers avec le statut `pending` dans la table `activation_submissions`. L'administrateur voit le nom, l'e-mail, le téléphone, le pays et les choix de simulation dans `/admin/waiting-list`, puis peut valider le dossier ; le client voit le statut en revenant sur `/activation`.
- Le client peut joindre jusqu'à quatre images d'exemple fictives (recto/verso du document et du justificatif), aux formats PNG, JPEG ou WebP, 2 Mo maximum chacune. Elles sont enregistrées avec le dossier et visibles uniquement aux administrateurs dans la liste d'attente. L'application valide le format du fichier, mais ne peut pas vérifier automatiquement que son contenu est fictif : ne jamais envoyer de vrai document, visage, nom, numéro ou renseignement personnel/bancaire.
- Dans le panneau `/admin`, l'action sur la carte active uniquement une carte de démonstration ; elle ne certifie pas une identité et ne représente pas un service bancaire réel.
- Le lien « Portefeuille démo » (`/investments`) donne accès à un portefeuille séparé du solde principal : graphiques animés avec un historique artificiel, cours simulés, crédits CRD sans valeur réelle, positions achetables/revendables et historique. Les courbes ne reprennent aucune donnée de marché réelle et ne promettent ni ne prédisent de rendement.
- L'administration peut générer un code à usage unique depuis « Codes portefeuille démo » (`/admin/demo-investment-codes`). Le code n'est affiché qu'une fois ; il crédite uniquement le portefeuille CRD, ne peut pas être retiré ni converti en euros et n'est jamais un dépôt d'argent.
- Aucun retrait réel ou frais/certification pour débloquer un retrait n'est proposé dans cette démonstration.

## Admin
- Ouvre `/admin` et utilise l'adresse définie par `ADMIN_USERNAME` ainsi que `ADMIN_PASSWORD` dans Render > Environment. Au démarrage, le mot de passe haché en base est synchronisé depuis cette variable.
- Pour créer ou réinitialiser l'administrateur en local, exécute `.\.venv\Scripts\python.exe -m flask --app app create-admin`. La commande demande l'adresse puis le mot de passe deux fois sans l'afficher. Dans le Shell Render, exécute `flask --app app create-admin`.
- Sur Render, `ADMIN_PASSWORD` est la source de vérité au démarrage. Si tu réinitialises le compte avec la commande CLI, mets aussi `ADMIN_PASSWORD` à la même valeur dans les variables du service, sinon le mot de passe défini dans Render reprendra effet au prochain redémarrage.
- `MAIL_USERNAME` et `MAIL_PASSWORD` servent uniquement à la connexion SMTP Gmail pour l'envoi d'e-mails. Ils ne servent pas à se connecter à `/admin`. N'utilise pas `Admin1234!` ni le mot de passe de Gmail comme mot de passe administrateur.
