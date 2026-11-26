# Planya — Registre des traitements et protection des données

Fiche établie sur le modèle du registre des activités de traitement (article 30 du RGPD).
Chaque mesure renvoie au fichier du dépôt qui l'applique : les règles décrites ici ne sont pas des intentions,
ce sont celles qui tournent en ligne.

*Mise à jour : novembre 2026.*

---

## 1. Responsable du traitement

| | |
|---|---|
| Responsable | Marine Zinzen, particulier, projet personnel et non commercial |
| Contact | adresse dédiée en cours de création (voir [section 9](#9-décisions-prises-et-mise-en-œuvre)) |
| Délégué à la protection des données | Non désigné (non obligatoire : ni organisme public, ni traitement à grande échelle, article 37) |
| Pourquoi un registre | L'exemption de l'article 30.5 (moins de 250 personnes) ne joue pas : le traitement n'est pas occasionnel. |

Les utilisateurs qui saisissent des informations sur leur groupe le font dans un cadre privé (exemption domestique, article 2.2.c) ;
l'éditrice reste responsable du traitement réalisé par l'application.

## 2. Traitement : « Organisation d'un séjour à plusieurs »

### Finalités

1. Créer un compte et s'y connecter.
2. Créer un séjour, y inviter des personnes, gérer les membres.
3. Organiser le séjour ensemble : planning, dépenses partagées, courses, corvées, informations pratiques, valise.
4. Animer le séjour : missions secrètes, sondages, classement, fil d'actu.
5. Prévenir les membres (notifications sur le téléphone, si la personne les active).
6. Assurer la sécurité du service (journaux techniques des hébergeurs).

### Bases légales (article 6)

| Finalités | Base légale | Pourquoi |
|---|---|---|
| 1 à 4 | Exécution du contrat (6.1.b) | La personne crée un compte et accepte les conditions d'utilisation pour utiliser ces fonctions. |
| 5 | Consentement (6.1.a) | Activées appareil par appareil, réglables par type, retirables à tout moment ([`js/notifications.js`](../js/notifications.js)). |
| 6 | Intérêt légitime (6.1.f) | Protéger le service et ses utilisateurs ; journaux limités et tenus par les hébergeurs. |

### Personnes concernées

Les utilisateurs inscrits, membres d'un ou plusieurs séjours.

### Catégories de données

| Donnée | Où | Visible par | Remarque |
|---|---|---|---|
| E-mail, mot de passe (haché) | Supabase Auth | Personne (pas même l'éditrice pour le mot de passe) | Seules données du compte. |
| Prénom | `sejour_membres` | Les membres du séjour | Choisi séjour par séjour : un même compte peut avoir un prénom différent dans chaque groupe. |
| Contenus partagés du séjour (activités, commentaires, dépenses, courses, corvées, sondages, publications) | Tables du séjour | Les membres du séjour | |
| Horaires et lieux d'arrivée et de départ | `trajets` | Les membres du séjour | Chacun ne modifie que les siens. |
| Billets de transport (fichiers) | Stockage `planya-billets` | **La seule personne qui les dépose** | Peuvent contenir nom, numéro de réservation, date de naissance : d'où l'accès strictement personnel. |
| Valise | `valise_objets` | **La seule personne concernée** | |
| Mission secrète, réponse, photo | `missions_secretes`, stockage `planya-missions` | **L'auteur** ; la cible, uniquement à partir du dernier jour du séjour | Le groupe ne voit qu'un total de missions réussies. |
| Votes | `sondage_votes` | Selon le sondage : soi seul (anonyme) ou les membres (visible) | En mode anonyme, la base ne fournit que des totaux. |
| Abonnement aux notifications de l'appareil | `abonnements_push` | La personne | Adresse technique fournie par le navigateur. |

Aucune donnée sensible au sens de l'article 9 n'est demandée. Les champs libres (notes, commentaires) pourraient en contenir
si un utilisateur en saisit : voir [section 9](#9-décisions-prises-et-mise-en-œuvre).

### Destinataires

- **Les membres du séjour**, pour les seules données partagées du séjour.
- **Sous-traitants** (article 28), qui hébergent sans exploiter :

| Sous-traitant | Rôle | Localisation des données |
|---|---|---|
| Supabase | Base de données, comptes, fichiers, fonctions | Stockage dans l'Union européenne (Stockholm, Suède) ; société américaine |
| GitHub (GitHub Pages) | Hébergement du site (fichiers statiques, sans données utilisateur) | États-Unis — adhérent au cadre de protection des données UE–États-Unis |
| Brevo | Envoi des e-mails de connexion (adresse e-mail) | France |
| Services de notification des navigateurs (Google, Apple, Mozilla) | Acheminement des notifications | Principalement États-Unis ; contenu chiffré de bout en bout (Web Push), ils ne peuvent pas le lire |

### Transferts hors Union européenne

- Hébergement du site par GitHub (États-Unis) : décision d'adéquation du cadre UE–États-Unis. Le site ne contient aucune donnée
  utilisateur ; seules les données techniques de connexion (adresse IP) transitent.
- Supabase stocke les données dans l'Union européenne, mais c'est une société américaine : un accès depuis les États-Unis
  (support, maintenance, demande d'une autorité) reste possible. Encadrement à vérifier dans son contrat de sous-traitance
  (clauses contractuelles types ou adhésion au cadre UE–États-Unis) : voir [section 9](#9-décisions-prises-et-mise-en-œuvre).
- Notifications : les services des navigateurs reçoivent l'adresse technique de l'appareil et un contenu chiffré qu'ils ne peuvent
  pas lire. Transfert limité, uniquement si la personne active les notifications.
- Aucune requête n'est envoyée à Google Fonts ni à un CDN : polices et bibliothèques sont hébergées avec le site
  ([`fonts/`](../fonts), [`js/vendor/`](../js/vendor)).

### Durées de conservation

| Donnée | Durée | Comment |
|---|---|---|
| Compte et données personnelles | Jusqu'à la suppression du compte, et au plus **1 an sans connexion** (avertissement par e-mail 1 mois avant) | Bouton « Supprimer mon compte » ([`sql/010_comptes.sql`](../sql/010_comptes.sql)) ; suppression des comptes inactifs : à mettre en place |
| Données d'une personne dans un séjour | Jusqu'à son départ du séjour | Effacées ou anonymisées par la base au départ (`depart_du_sejour`, [`sql/012_fil.sql`](../sql/012_fil.sql)) |
| Fichiers (billets, photos de missions) | Effacés dans l'heure qui suit la suppression de la donnée | File `fichiers_a_effacer` + fonction planifiée [`nettoyer-fichiers`](../supabase/functions/nettoyer-fichiers/index.ts) |
| Notifications | Effacées dès l'envoi | [`supabase/functions/planya-push`](../supabase/functions/planya-push/index.ts) |
| Historique des tâches planifiées | 7 jours | Purge automatique hebdomadaire |
| Séjour et contenus partagés | Jusqu'à la suppression du séjour par l'organisation, et au plus **1 an après sa date de fin** (membres prévenus 1 mois avant) | Supprimer le séjour efface tout son contenu en cascade ; suppression automatique : à mettre en place |
| Contenus utiles au groupe d'une personne qui part (dépenses, activités, sondages créés) | Comme le séjour | Conservés **anonymisés** (« Ancien membre ») pour ne pas fausser les comptes des autres |
| Journaux techniques (adresse IP) | Durées fixées par les hébergeurs | Tenus par Supabase et GitHub, non exploités |

## 3. Protection des données dès la conception (article 25)

| Principe | Mesure | Où |
|---|---|---|
| Cloisonnement | Chaque table est protégée par des règles de sécurité au niveau des lignes (Row Level Security) : on ne lit un séjour que si on en est membre. L'interface ne peut pas contourner ces règles. | [`sql/`](../sql) (toutes les tables) |
| Aucun accès anonyme | Aucun droit pour les visiteurs non connectés, sur aucune table. | `revoke … from anon` dans chaque script |
| Moindre privilège | Les droits d'écriture sont accordés colonne par colonne ; les actions sensibles (créer, rejoindre un séjour, enregistrer une dépense, voter, tirer une mission) passent par des fonctions de la base qui vérifient l'identité et le rôle. | [`sql/001_sejours.sql`](../sql/001_sejours.sql), [`003`](../sql/003_depenses.sql), [`008`](../sql/008_missions.sql), [`009`](../sql/009_sondages.sql) |
| Minimisation | Le compte ne contient que l'e-mail. Le prénom est donné par séjour. Les événements du fil ne stockent aucun prénom (affiché à la lecture) et une mission réussie est annoncée sans son contenu. | [`sql/012_fil.sql`](../sql/012_fil.sql) |
| Confidentialité par défaut | Billets, valise et mission du jour : visibles par la seule personne concernée. Sondages anonymes par défaut, anonymes aussi dans la base. | [`006`](../sql/006_voyage.sql), [`007`](../sql/007_valise.sql), [`008`](../sql/008_missions.sql), [`009`](../sql/009_sondages.sql) |
| Intégrité | Une dépense n'est enregistrée que si la somme des parts égale le montant (en centimes). Les événements du fil sont écrits par la base, impossibles à falsifier depuis l'appli. | [`003`](../sql/003_depenses.sql), [`012`](../sql/012_fil.sql) |
| Pas de secret dans le code | Les clés d'envoi des notifications sont générées par la fonction elle-même et rangées dans le coffre-fort de Supabase (Vault). | [`sql/013_notifications.sql`](../sql/013_notifications.sql) |
| Aucune donnée personnelle dans le dépôt | Le code est public ; les données sont dans la base. | — |

## 4. Mesures de sécurité (article 32)

- Chiffrement des échanges (HTTPS) ; mots de passe hachés, jamais stockés en clair (Supabase Auth).
- Règles d'accès testées avec plusieurs comptes avant chaque mise en ligne (membre, non-membre, organisation, visiteur anonyme).
- Espaces de stockage privés ; liens d'ouverture temporaires (1 heure).
- Fonctions de la base en `security definer` avec `search_path` vide, pour éviter le détournement de leurs droits.
- Aucun traceur, aucun cookie publicitaire ni de mesure d'audience.

## 5. Droits des personnes (articles 15 à 21)

| Droit | Mise en œuvre |
|---|---|
| Accès et portabilité (15, 20) | « Mon compte » → **Télécharger mes données** : fichier JSON lisible par d'autres outils ([`js/legal.js`](../js/legal.js)). |
| Rectification (16) | Chacun modifie directement ses contenus et son prénom. |
| Effacement (17) | « Mon compte » → **Supprimer mon compte**, immédiat. Quitter un séjour efface ses données de ce séjour. |
| Retrait du consentement (7.3) | Notifications désactivables à tout moment, par appareil et par type. |
| Opposition (21) | Ne s'applique qu'aux journaux techniques (intérêt légitime) : demande à l'adresse de contact. |
| Limitation (18) | Sur demande à l'adresse de contact. |
| Information (13) | Politique de confidentialité et mentions légales, accessibles sans compte ([`js/legal.js`](../js/legal.js)). |

## 6. Cas particulier : la suppression d'un compte

Supprimer un compte dans une application collective pose une question juridique concrète : effacer **tout** fausserait les
comptes des autres (une dépense disparaîtrait des soldes). Le choix retenu :

- **effacé** : compte, prénoms, valise, billets, trajets, missions, votes, commentaires, publications, inscriptions ;
- **effacé aussi** : les missions *des autres* qui visaient la personne, car leur texte contient son prénom ;
- **anonymisé** : ce qui sert au groupe (dépenses, activités, courses, sondages créés). La personne n'y apparaît plus que comme
  « Ancien membre », sans lien possible avec son identité, puisque le compte et le prénom n'existent plus ;
- **transmis** : si la personne organisait seule un séjour, l'organisation passe au membre le plus ancien ; si elle y était seule,
  le séjour est supprimé.

Détail : [`sql/010_comptes.sql`](../sql/010_comptes.sql), [`sql/012_fil.sql`](../sql/012_fil.sql).

## 7. Analyse d'impact (article 35)

Au regard des neuf critères du CEPD (lignes directrices WP248), le traitement ne paraît pas en remplir deux :

- **évaluation ou notation** : il existe un classement en points, mais c'est un jeu entre membres d'un même séjour, sans effet
  en dehors du groupe ; il ne vise pas les aspects que ce critère protège (travail, situation économique, santé, comportement…) ;
- pas de surveillance systématique (les horaires de voyage sont saisis volontairement, sans géolocalisation) ;
- pas de données sensibles demandées, pas de traitement à grande échelle, pas de croisement de fichiers ;
- pas de personnes vulnérables visées : l'application est réservée aux personnes majeures ;
- pas de technologie innovante ni de décision automatisée produisant des effets juridiques.

Une analyse d'impact ne paraît donc pas obligatoire. Elle est **réalisée quand même**, à titre de bonne pratique
(méthode CNIL), et sera réexaminée si l'application grandit ou ajoute des fonctions (messagerie, géolocalisation, photos partagées).

## 8. Violation de données (articles 33 et 34)

En cas de violation : évaluation du risque, notification à la CNIL sous 72 heures si un risque existe, information des personnes
si le risque est élevé, et inscription au registre des violations. Les journaux de Supabase servent à l'analyse.

## 9. Décisions prises et mise en œuvre

| Sujet | Décision | État |
|---|---|---|
| Base légale principale | Exécution du contrat (6.1.b) | En place : [conditions d'utilisation](../js/legal.js), acceptées à l'inscription (version et date conservées) |
| Notifications | Consentement (6.1.a), avec l'article 82 de la loi Informatique et Libertés pour l'accès à l'appareil | En place |
| Âge minimum | **18 ans** : pleine capacité à conclure le contrat ; case à cocher à l'inscription | En place |
| Comptes inactifs | Suppression après **1 an** sans connexion, avertissement 1 mois avant | À mettre en place |
| Séjours terminés | Suppression **1 an** après la date de fin, membres prévenus 1 mois avant | À mettre en place |
| Analyse d'impact | Non obligatoire, réalisée quand même | À rédiger |
| Données de la version bêta (« Saraillon », août 2026) | Archive remise à l'éditrice, puis suppression de la base : plus de finalité justifiant leur conservation | À faire |

Restent aussi à faire avant une ouverture large au public :

- **Adresse de contact dédiée** pour les demandes RGPD (emplacement prévu : `CONTACT_EMAIL` dans [`js/config.js`](../js/config.js)).
- **Contrats de sous-traitance** (article 28) : accepter et archiver les DPA de Supabase, GitHub et Brevo, et vérifier
  l'encadrement des transferts vers les États-Unis (Supabase, services de notification).
- **Champs libres** : rappeler dans l'interface de ne pas y saisir de données sensibles (santé, allergies…).
