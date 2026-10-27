# The Wall

Application mobile de réseau social développée en **Flutter** avec **Supabase** (authentification, base PostgreSQL, temps réel et stockage de fichiers). Les utilisateurs créent un compte, consultent un fil de publications, ajoutent des amis et échangent en messagerie privée, avec envoi d'images.

> Projet académique réalisé en binôme dans le cadre de notre apprentissage de Flutter.

## Fonctionnalités

### Authentification et session

- Inscription avec nom, prénom, nom d'utilisateur, e-mail et mot de passe ; le profil est créé dans la table `profiles`.
- Connexion par e-mail et mot de passe, session conservée entre deux lancements.
- Redirection automatique vers l'accueil ou l'écran de connexion selon l'état de la session.
- Statut **en ligne / hors ligne** mis à jour à l'ouverture et à la fermeture de l'application.
- Déconnexion.

### Fil de publications

- Fil des publications de tous les utilisateurs, du plus récent au plus ancien, mis à jour en temps réel.
- Auteur affiché avec sa photo de profil et son statut en ligne.
- « J'aime » sur une publication (un seul par utilisateur), avec compteur.

### Profil

- Photo de profil, nom d'utilisateur et statut en ligne.
- Nombre d'amis.
- Liste de ses propres publications avec leur nombre de « J'aime ».

### Amis

- Liste des utilisateurs qui ne sont pas encore amis, avec leur photo.
- Envoi d'une demande d'ami, sans doublon possible.
- Page des demandes envoyées et reçues : accepter (l'amitié est enregistrée dans les deux sens) ou refuser.

### Messagerie privée

- Liste des conversations avec ses amis, avec aperçu du dernier message et nombre de messages non lus.
- Conversation privée entre deux amis.
- Envoi de texte et d'**images** depuis la galerie, stockées dans Supabase Storage.
- Accusé de lecture : les messages passent à « vu » à l'ouverture de la conversation.
- Modification et suppression de ses propres messages.

## Contributeurs

| Contributeur | Contact | Partie réalisée |
| --- | --- | --- |
| **Zakaria El Aoufir** | [zakaria.elaoufir@gmail.com](mailto:zakaria.elaoufir@gmail.com) | Messagerie privée (conversations, images, messages lus, modification et suppression) et gestion des amis (recherche, demandes, acceptation et refus) |
| **Othmane** | [othmaneal.pro@gmail.com](mailto:othmaneal.pro@gmail.com) | Authentification (inscription, connexion, session) et fil de publications (affichage, « J'aime ») |

## Stack technique

- **Flutter / Dart**
- **Supabase** :
  - Auth : e-mail et mot de passe
  - Base PostgreSQL et flux temps réel (`stream`)
  - Storage : photos de profil et images des messages
- `image_picker` pour choisir une image dans la galerie
- `intl` pour le formatage des dates

## Structure du projet

```text
lib/
├── main.dart                     # Initialisation Supabase, statut en ligne, routes
├── session_manager.dart          # Passage en ligne / hors ligne
├── auth/
│   ├── auth.dart                 # Écoute l'état de la session
│   └── login_or_register.dart    # Bascule connexion / inscription
├── components/                   # Bouton, champ de texte, carte de publication
└── pages/
    ├── login_page.dart
    ├── register_page.dart
    ├── home_page.dart            # Fil de publications et « J'aime »
    ├── profile_page.dart         # Profil, nombre d'amis, mes publications
    ├── add_friends_page.dart     # Utilisateurs à ajouter
    ├── follow_request_page.dart  # Demandes d'amis
    ├── messages_page.dart        # Liste des conversations
    └── chat_page.dart            # Conversation privée
```

## Modèle de données Supabase

| Table | Contenu |
| --- | --- |
| `profiles` | `id` (lié à Auth), `email`, `nom`, `prenom`, `username`, `avatar_url`, `online`, `created` |
| `publications` | `id`, `profile_id`, `content`, `likes`, `created_at` |
| `publication_likes` | `publication_id`, `profile_id` |
| `friends` | `user_id`, `friend_id` (une ligne par sens) |
| `follow_requests` | `id`, `sender_id`, `receiver_id`, `status`, `created_at` |
| `messages` | `id`, `sender_id`, `receiver_id`, `content`, `image_url`, `seen`, `created_at` |

Également utilisés :

- la fonction SQL `update_publication_likes(pub_id)`, qui met à jour le compteur de « J'aime » ;
- les buckets Storage `profile-pictures` et `chat-pictures`.

Activer la Row Level Security (RLS) et définir les politiques d'accès sur chaque table.

## Installation

### Prérequis

- Flutter SDK (Dart 3.9 ou plus récent)
- Un projet Supabase contenant les tables, la fonction et les buckets ci-dessus

### Lancement

```bash
git clone https://github.com/ZakariaElAoufir/TheWall_Flutter.git
cd TheWall_Flutter
flutter pub get
```

Renseigner l'URL et la clé publique (`anonKey`) de votre projet Supabase dans `lib/main.dart` :

```dart
await Supabase.initialize(
  url: 'https://VOTRE-PROJET.supabase.co',
  anonKey: 'VOTRE-ANON-KEY',
);
```

Puis lancer l'application :

```bash
flutter run
```
