# 🐧 TP Cybersécurité : Identifier les services réseau sur un serveur Linux

Dans le cadre de ma formation en cybersécurité, j'ai réalisé un TP centré sur l'identification des services actifs sur une machine Linux et le test de ces services au niveau réseau. Voici un résumé de la démarche et des concepts abordés (sans commandes ni résultats détaillés, pour des raisons évidentes de sécurité).

##  Objectifs du TP

- Identifier les processus et services qui tournent en arrière-plan sur un système Linux
- Faire le lien entre un processus (PID) et un port réseau ouvert
- Comprendre comment un service répond à une connexion réseau brute
- Mesurer les risques liés à l'utilisation de protocoles non chiffrés

##  Partie 1 — Repérer les services actifs

Un serveur, au sens informatique, est simplement un programme qui attend une demande (requête) et y répond. Sous Linux, ces programmes apparaissent comme des **processus**, chacun identifié par un numéro unique appelé **PID** (Process ID).

Pour explorer ces processus, deux angles sont possibles :

1. **La liste des processus** : elle permet de voir tous les programmes en cours d'exécution, avec leur propriétaire (utilisateur), leur PID, et le processus qui les a lancés (PPID). Certaines actions nécessitent des droits administrateur (`root`) car tous les processus ne sont pas visibles avec un compte utilisateur classique.

2. **Les connexions réseau actives** : un outil dédié permet de savoir quels ports sont ouverts, quel protocole est utilisé, et quel processus est responsable de cette écoute. C'est ce qui permet de faire le lien entre « un port est ouvert » et « quel programme exact l'utilise ».

Voici, à titre d'exemple, la structure d'une sortie type (valeurs fictives, uniquement pour illustrer les colonnes) :

| Protocole | Adresse locale | Port | État      | PID  | Programme |
|-----------|-----------------|------|-----------|------|-----------|
| TCP       | 0.0.0.0         | 80   | LISTEN    | xxx  | serveur web |
| TCP       | 0.0.0.0         | 22   | LISTEN    | xxx  | service SSH |
| TCP       | 0.0.0.0         | 21   | LISTEN    | xxx  | service FTP |

**Enseignement clé :** un simple numéro de port ne garantit rien. Le port 80 est *conventionnellement* associé au web, mais rien n'empêche un programme malveillant de se faire passer pour un serveur légitime en portant le même nom ou en écoutant le même port. Il faut donc croiser plusieurs sources d'information (liste des processus + connexions réseau) pour confirmer la nature réelle d'un service.

##  Partie 2 — Tester un service directement au niveau TCP

Une fois qu'un service est identifié comme actif sur un port donné, il est possible de dialoguer directement avec lui via une connexion brute, sans passer par un client dédié (navigateur, client SSH, etc.). Cela permet de vérifier "à la main" ce que répond réellement le service, plutôt que de se fier uniquement à son nom.

Quelques observations générales tirées de cet exercice :

- Un serveur web, interrogé avec des données qu'il ne comprend pas, renvoie une **erreur formatée comme une page web** — cohérent avec sa fonction, et cela permet au passage de révéler sa version logicielle.
- Un service comme SSH réagit différemment : il annonce sa version dès la connexion, puis ferme la session si les données envoyées ne respectent pas son protocole.
- Certains ports correspondent à des services qui n'acceptent pas ce type de connexion (protocole différent, ex. UDP), et le comportement observé est alors très différent (pas de réponse ou connexion refusée).

**Point de vigilance sécurité :** l'outil utilisé pour ce test transmet toutes les données **en clair**, mots de passe compris s'il y en avait. Il est très utile pour du diagnostic rapide, mais ne doit jamais être utilisé pour administrer un serveur à distance — un protocole chiffré est la seule option acceptable pour cela.

##  Compétences travaillées

- Analyse de processus systèmes sous Linux
- Cartographie des services réseau actifs (ports / protocoles / processus)
- Reconnaissance manuelle de services via une connexion TCP brute
- Sensibilisation aux risques des protocoles non chiffrés
- Esprit critique : ne jamais faire confiance à un nom de processus ou un numéro de port sans vérification

##  Ce que je retiens

Ce TP m'a permis de comprendre concrètement pourquoi, en cybersécurité, on ne se contente jamais d'une seule source d'information pour qualifier un service. Croiser plusieurs outils (processus, connexions réseau, réponse applicative) est une démarche de base pour tout analyste qui doit auditer une machine ou détecter une activité suspecte.

---
*TP réalisé dans le cadre de ma formation en cybersécurité (environnement de laboratoire type CyberOps).*
