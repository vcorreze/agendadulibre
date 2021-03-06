Production : Astuces
=====================

Vérifier la locale
----------------------
    
    ```
    $ locale -a
    C
    C.UTF-8
    fr_FR.utf8
    POSIX
    ```

Il faut s'assurer que la locale définie dans le fichier `agenda/events/templatetags/humantime.py` (ligne 34, 
utilisé pour l'affichage des dates lors de la consultation d'un événement) soit bien une des locales 
disponible sur le serveur.

La page suivante explique l'erreur rencontrée par Python si cette condition n'est pas respectée, et donne des 
informations sur la consultation et l'ajout de nouvelles locales sur Debian.
https://stackoverflow.com/questions/14547631/python-locale-error-unsupported-locale-setting


Activer le mode debug en production
-------------------------------------

En configuration de production, Django n'affiche pas le détail des erreurs 500 à l'utilisateur mais une page 
personnalisée n'incluant pas d'information critique : `agenda/templates/500.html` (c'est le même principe pour 
les erreurs 404 : `agenda/templates/404.html`). Par ailleurs Django envoie le détail de l'erreur par 
email à l'administrateur du site (défini dans agenda/settings.py > ADMINS).

Pour avoir le détail des erreurs affichées dans le navigateur, il faut modifier la variable DEBUG dans le fichier
agenda/production.py : `DEBUG = True`

Si l'envoi des emails est mal configuré, il est possible de rediriger l'écriture des emails dans les logs uniquement 
en ajoutant la variable suivante dans le fichier agenda/production.py : `EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'`.
Les emails envoyés sont alors écrits dans le fichier var/log/uwsgi.log

Démarrer supervisord au boot
----------------------------

Pour que l'agenda démarre au boot, il faut lancer supervisord via le cron de l'utilisateur. Il suffit de coller la
ligne suivante dans le cron de l'utilisateur choisi (`crontab -e`) :

```
@reboot /home/{myuser}/agendaEteAccoord/bin/supervisord -c /home/{myuser}/agendaEteAccooord/parts/supervisor/supervisord.conf 2>&1
```
:sparkles:
