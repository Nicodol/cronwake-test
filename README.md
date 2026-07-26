# Test manuel de Cronwake, version 5-10 minutes

Repo de test pour valider Cronwake de zéro, depuis le compte perso. 4 workflows :
3 avec un cron (toutes les 5 min ; tous les jours 07:00 UTC ; toutes les heures à HH:13)
et 1 SANS cron (il ne doit jamais apparaître dans Cronwake).

La base Cronwake a été vidée : tu pars vraiment de zéro.

---

## Étape 0 (30 s) : repartir de zéro côté GitHub

Va sur https://github.com/settings/installations : si « cronwake » apparaît
(il y a 2 anciennes installations), ouvre **Configure** puis tout en bas **Uninstall**
pour chacune. Sans ça, ton test ne part pas d'une vraie page blanche.

## Étape 1 (1 min) : installer

1. Va sur https://cronwake.com et clique **Install on GitHub**.
2. Choisis ton compte perso, puis **Only select repositories** → ce repo
   (`cronwake-test`) → **Install**.

**Ce que tu dois voir** : l'écran GitHub liste des permissions UNIQUEMENT en
lecture (read). C'est l'argument n°1 du produit : vérifie-le en vrai.

## Étape 2 (1 min) : la découverte

Retour sur cronwake.com → **Sign in with GitHub** → le dashboard s'ouvre.

**Ce que tu dois voir** :
- **3 crons découverts** (`every-5-min`, `daily-backup-sim`, `hourly-sync-sim`),
  sans avoir rien configuré : c'est le « zero-config ».
- `no-cron` **absent** de la liste (pas de ligne cron = pas surveillé, pas de bruit).
- `daily-backup-sim` surveillé alors qu'il n'a **jamais tourné** (prochaine échéance
  demain 07:00 UTC) : c'est la couverture « jour zéro ».

## Étape 3 (2 min) : le canal d'alerte

1. Va dans **Settings** (menu en haut) → colle l'URL de ton webhook Slack
   (#cron-alerts) ou Discord → **Save**.
2. Clique **Send test alert**.

**Ce que tu dois voir** : l'alerte de test arrive dans ton canal en quelques
secondes. (Au passage : configurer un canal = c'est la condition du statut
« founding org » moitié prix à vie.)

## Étape 4 (2-3 min) : les réglages par job + le badge

Clique sur `every-5-min` :
1. **Grâce** : mets-la à 2 minutes (au lieu de 10 par défaut). Comme GitHub
   sous-exécute notoirement les crons */5, ça accélère la première vraie détection.
2. **Tag** : ajoute un tag (ex. `test`), retourne au dashboard et filtre dessus.
3. **Badge** : copie l'URL du badge et ouvre-la en navigation privée : le SVG
   d'état est public, sans login.

## Étape 5 (1 min à déclencher, résultat en ~10-30 min) : le « silently disabled »

Dans CE repo GitHub → onglet **Actions** → workflow `every-5-min` → menu « ... » →
**Disable workflow**. C'est exactement ce que GitHub fait en douce après 60 jours
d'inactivité, sauf que là c'est toi qui le provoques.

**Ce que tu dois voir** (dans la demi-heure, sur ton canal) : une alerte
« silently disabled ». Aucun autre outil ne détecte ça factuellement.

## Pendant ce temps (rien à faire)

- Le cron `*/5` va rater des créneaux tout seul (GitHub le sous-exécute vraiment) :
  avec la grâce à 2 min, tu devrais voir un **late** ou **missed** apparaître dans
  les ~10-20 minutes suivant l'installation, et l'alerte tomber sur ton canal.
- Demain : si tu as activé le **digest quotidien** dans Settings, tu recevras le
  « tes N crons, tous verts » (ou pas tous verts) : c'est le signal de vie.

## Résumé : ce qui est validé en 10 minutes

| # | Test | Promesse validée |
|---|---|---|
| 1 | Install repos choisis, scopes read-only | Sécurité, zero-config |
| 2 | 3 crons trouvés, no-cron ignoré, jour zéro | Découverte org-wide, couverture immédiate |
| 3 | Canal + alerte test reçue | La chaîne d'alerte de bout en bout |
| 4 | Grâce, tag, badge public | Les réglages self-serve |
| 5 | Disable → alerte | La détection du « silencieusement désactivé » |
| auto | */5 sous-exécuté → late/missed | LA promesse centrale, sur du vrai GitHub |

Quand tu as fini : tu peux laisser le repo installé (il continuera d'accumuler de
l'historique réel, utile pour la démo), ou désinstaller en un clic.
