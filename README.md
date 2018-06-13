# epsi-ping-count

Bonjour,

Dans le cadre du cours sur Docker, nous allons nous mettre dans des conditions proches de celles du monde de l’entreprise afin de mieux comprendre et assimiler les enjeux du travail en équipe en monde professionnel. Pour cela, je vous demande d’ici le prochain cours de développer une mini application web appelée PingCount ainsi qu’un (ou des) script(s) de validation en Bash.

Vous avez la liberté du langage / techno à utiliser mais voici les contraintes :

L’application doit être exécutable depuis un environnement Ubuntu Server 16.04 (Xenial)
L’application doit pouvoir s’interfacer à Sentry. Voir les clients officiels disponibles : https://docs.sentry.io/ (il y en a d’autres non officiels pour d’autres langages).

 ---
 
En réponse à une question à propos de Sentry, voici quelques précisions :

L’application demandée doit exposer une API REST, et a besoin pour cela d’embarquer un serveur web qui répond aux 2 routes demandées : /ping et /count (normalement vous allez utiliser un framework qui fait tout ce travail pour vous)
L’application a donc pour vocation a être lancée en mode serveur qui se met en attendre des requête à traiter
Dans ce genre de cas c’est une bonne pratique que d’afficher un message de log quand l’application est lancée afin de confirmer qu’elle est en écoute
Idem quand elle se termine (réception de CTRL+C) afin d’informer qu’elle cesse de servir des requêtes
 

Dans notre cas, Sentry est juste une lib de log à utiliser à cette fin plutôt que de simplement afficher des messages à l’écran. Donc quand on lance l’application, elle doit envoyer un message à Sentry, se mettre en écoute de requêtes, et envoyer un autre message à Sentry quand on l’arrête (via CTRL+C).

---

Suite à une autre demande de précisions : non il ne faut pas livrer l’application sous Docker (nous n’avons pas encore vu comment faire !). Le code source de l’application + les scripts Bash et la documentation éventuelle doivent être livrés dans une répertoire portant le numéro de votre binôme sur le projet Gitlab de votre groupe. Le code doit pouvoir tourner sur Ubuntu 16.04 (celui-là même que vous avez installé dans une VM avec moi).

 

Nous verrons justement dans le prochain cours comment mettre cette application sous Docker. C’est pourquoi elle doit avoir été développée avant le cours.

---

Pour rappel, pour notre prochain cours, vous devez avoir terminé de « dockeriser » votre application PingCount ainsi que le script de test dans GitLab. C.à.d qu’il doit être possible de faire un « docker pull » sur vos 2 conteneurs afin de les lancer localement sur une machine disposant de Docker.

De plus, assurez-vous d’avoir une VM Ubuntu 16.04 de fonctionnelle avec Docker et Docker-Compose d’installés dedans (ou environnement compatible). Pour rappel, la procédure d’installation de Docker :

```
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
docker info (pour tester après s’être relogué)
```

Pour Docker-Compose:
```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.20.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose –version (pour tester)
```

---

Je vous rappelle ce qui est attendu concernant le TP à rendre sur la partie Docker Compose :

Enrichir votre docker-compose.yml initial (qui combine votre programme de Test avec votre PingCount) pour tester 5 projets supplémentaires (soient 6 au total) dans l’ordre qui suit votre propre position dans le tableau : https://gitlab.com/epsi4/epsi-l4-c2/blob/master/projets-docker.md
Vous devez avoir terminé cette partie pour vendredi 15 au soir et m’envoyer par email un compte rendu qui contient :
Le fichier docker-compose.yml utilisé (ou un lien vers son contenu dans votre repo gitlab)
La liste des applications testées avec précision si le test a été considéré comme valide ou non par votre test
Pour chaque application non conforme identifiée, créez un ticket de bug dans le repository concerné, affectez le à un des membres du binôme associé, et donnez moi un lien vers ce ticket dans votre email
Veillez à rédiger vos éventuels tickets de bug avec professionnalisme : donnez tous les éléments nécessaires pour reproduire le problème et indiquer ce qui est attendu, de façon factuelle
Ce faisant, vous devriez tous être informés d’ici vendredi soir si votre propre application passe avec succès les tests rédigés par vos collègues, et sinon quels sont les soucis
Si tout se passe bien, bravo vous avez terminé votre TP
Sinon, il faudra corriger votre application en conséquence et résoudre les tickets qui vous ont été assignés pour lundi 18 au soir au plus tard
Comme pour le précédent module, une partie importante de la note prendra en compte le fait que d’autres groupes sont parvenus à réutiliser votre travail avec succès. Donc si à l’issue de lundi prochain votre application ne passe pas les tests alors qu’elle le devrait, vous serez pénalisés en conséquences. Et si votre code de test invalide des applications alors qu’il ne devrait pas, ce sera aussi pénalisant.
 
En espérant que ce travail d’échange entre vous vous aidera à saisir de façon plus concrète les challenges soulevés par le partage de code entre équipes d’une part, et l’immense bénéfice de pouvoir rapidement livrer une version corrigée (grâce à une intégration continue de qualité) quand la date de livraison finale approche et qu’il faut corriger des problèmes inattendus alors qu’on est déjà à la bourre 😊

Autre rappel : il n’est pas nécessaire d’avoir une section « ports » dans votre docker-compose.yml afin de pouvoir « pinger » les applications testées :

```yml
services:
  pingcount:
    image: registry.gitlab.com/xxx/pingcount:master
    ports:
      - 5000:5000
  test:
    image: registry.gitlab.com/xxx/testpingcount:master
    depends_on:
      - pingcount
    environment:
      URL: http://pingcount:5000
```

En effet, grâce à la section « depends_on » déclarée au niveau de chaque service de test associé, ce dernier aura directement accès au port de l’application testée sans avoir à l’exposer de façon explicite (car visible sur un réseau privé).

La section « ports » serait utile si on désirait pouvoir accéder à l’application depuis la machine hôte, mais là le fait qu’elle soit isolée dans un réseau privé « invisible » en dehors du docker compose nous convient parfaitement : de cette façon vous n’aurez aucun problème à instancier plusieurs conteneurs qui utilisent le même numéro de port.
