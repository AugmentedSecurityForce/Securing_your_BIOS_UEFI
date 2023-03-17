- [OBJET](#objet)
- [DEFINITIONS](#definitions)
- [GPO ET INTUNE](#gpo-et-intune)
- [LISTE DES RECOMMANDATIONS](#liste-des-recommandations)
  - [RECOMMANDATIONS GENERIQUES](#recommandations-generiques)
    - [MOT DE PASSE DU BIOS](#mot-de-passe-du-bios)
    - [ORDRE DE DEMARRAGE](#ordre-de-demarrage)
    - [ACTIVATION DES PERIPHERIQUES](#activation-des-peripheriques)
    - [SECURE BOOT](#secure-boot)
  - [PROTECTIONS](#protections)
    - [AMD XD / INTEL NX](#amd-xd--intel-nx)
    - [INTEL SGX](#intel-sgx)
    - [TPM](#tpm)
      - [TPM 2.0](#tpm-20)
    - [PERFORMANCE](#performance)
  - [VIRTUALISATION](#virtualisation)
  - [DEMARRAGE](#demarrage)
    - [WAKE ON LAN](#wake-on-lan)
    - [WAKE ON TIMER](#wake-on-timer)
  - [COMMUNICATIONS RESEAU](#communications-reseau)
    - [COMMUTATION SANS FIL](#commutation-sans-fil)
    - [CONTROLEUR D'INTERFACE RESEAU (NIC)](#controleur-dinterface-reseau-nic)
  - [CONFIGURATION EQUIPEMENTS](#configuration-equipements)
    - [SATA OPERATION](#sata-operation)
    - [USB](#usb)
  - [HEALTHCHECK](#healthcheck)
    - [SMART](#smart)
  - [MISE A JOUR ET DOWNGRADE](#mise-a-jour-et-downgrade)
    - [PROCEDURE UPDATE / DOWNGRADE](#procedure-update--downgrade)
    - [UEFI ENCAPSULATION POUR MISE A JOUR DE FIRMWARE](#uefi-encapsulation-pour-mise-a-jour-de-firmware)
  - [COMPUTRACE](#computrace)

# OBJET
Ce document a pour objectif de vous fournir des informations concernant le durcissement du BIOS de vos postes de travail.
Il conviendra d'adapter chaque réglage à vos besoins et à la population de votre parc.

* Dans le reste du document le terme BIOS sera utilisé pour parler aussi bien du BIOS que de l'UEFI.
* L'analyse a été faite sur un poste Dell, certains termes ou options peuvent varier ou ne pas être disponible sur votre poste.
* L'analyse se concentre sur les options ayant un impact sur la sécurité du poste.

# DEFINITIONS
* BIOS : le BIOS (Basic Input/Output System) est un logiciel qui contrôle les fonctions de base d'un ordinateur. Il gère les interactions entre le système d'exploitation, les périphériques et les composants matériels de l'ordinateur. Le BIOS peut être considéré comme la clé de voûte de l'ensemble du système, car il est responsable du démarrage initial du système d'exploitation.
* UEFI : Le standard UEFI (de l’anglais Unified Extensible Firmware Interface, signifiant en français : « Interface micrologicielle extensible unifiée ») définit une interface entre le micrologiciel (firmware) et le système d'exploitation (OS) d'un ordinateur. Cette interface succède sur certaines cartes-mères au BIOS.

![BIOS](./FR/Images/BIOS.png)
![UEFI](FR/Images/UEFI.png)

# GPO ET INTUNE
Si votre entreprise dispose d'un SOC, il est possible d'utiliser des outils fournis par les constructeurs.
C'est le cas pour des ordinateurs des marques Dell, HP et Lenovo.
_Si le renouvellement de votre parc est à l'étude, cela peut être un point à prendre en compte_

Pour Dell : 
* Dell Command PowerShell : https://www.dell.com/support/kbdoc/fr-fr/000177240/dell-command-powershell-provider
* Dell BIOS Provider : https://www.powershellgallery.com/packages/DellBIOSProvider/2.1.0 

Pour HP : 
* https://www.powershellgallery.com/packages/HPEBIOSCmdlets/3.0.0.0

Pour Lenovo : 
* https://support.lenovo.com/us/en/solutions/ht100612-lenovo-bios-setup-using-windows-management-instrumentation-deployment-guide-thinkpad
* https://download.lenovo.com/pccbbs/thinkcentre_pdf/mvdeploy_en.pdf 

# LISTE DES RECOMMANDATIONS
## RECOMMANDATIONS GENERIQUES
### MOT DE PASSE DU BIOS
Il est important de mettre un mot de passe pour accéder à la configuration du BIOS afin de s’assurer que seuls les personnels habilités puissent consulter et modifier les réglages.

Si un attaquant peut accéder au BIOS sans autorisation, il peut modifier ses paramètres pour compromettre le système.

Par exemple, il peut configurer le BIOS pour démarrer à partir d'un périphérique malveillant ou modifier des paramètres qui désactivent des fonctions de sécurité importantes du système.

C'est pourquoi il est recommandé : 
* Mettre en place un mot de passe fort,
* Eviter de mettre le même mot de passe sur toute votre flotte de terminaux,
* Prendre en compte que les terminaux mobiles sont plus soumis à la perte / vol lors de la définition du mot de passe.
### ORDRE DE DEMARRAGE
L'ordre d'amorçage identifie à l'ordinateur les périphériques qui pourraient être autorisés. L'ordinateur va rechercher chaque périphérique identifié séquentiellement jusqu'à ce qu'il trouve une partition amorçable active. À ce moment-là, l'ordinateur tenter de démarrer le système ou l'utilitaire trouvé sur le premier périphérique disponible.

Dans de nombreux cas, le démarrage sur le disque dur est la dernière option afin de permettre un démarrage réparateur via USB ou PXE.

Voici quelques exemples des dangers à laisser le choix d'ordre du démarrage :
* Installation de logiciels malveillants ou moyen de persistances : l'attaquant peut déployer des logiciels malveillants sur l'ordinateur via un démarrage sur un autre lecteur. Ces logiciels peuvent causer des dommages importants à l'ensemble du système informatique de l'entreprise lors du démarrage normal suivant (exemple : dépôt de fichier malveillant, modification de clef de registre, etc.).
* Vol de données : l'attaquant démarre à partir d'un disque amovible pour copier des données confidentielles de l'entreprise et les emporter avec lui.

C'est pourquoi il est recommandé : 
* Seule la partition sur laquelle le système est installé doit être amorcée.
### ACTIVATION DES PERIPHERIQUES
La plupart des ordinateurs modernes permettent à l'administrateur du BIOS de déterminer exactement les fonctionnalités des périphériques à exposer au système d'exploitation et, par conséquent, à l'utilisateur.

Par exemple via cette option il est possible de désactiver les périphériques suivants : 
* IDE/SATA,
* USB / Carte SD,
* Périphériques réseau (Wi-Fi, Bluetooth, NFS, GPS, modem, etc.)
* Micro / caméra

C'est pourquoi il est recommandé :
* Ne laisser activer que les équipements nécessaires et supporté par votre politique d'entreprise.
  * Garder en mémoire que la réactivation nécessitera d'aller dans le BIOS et donc qu'un utilisateur standard ne pourra le faire.
### SECURE BOOT
En théorie cette option est toujours activé sur les postes récent car il est nécessaire pour un démarrage UEFI.

Le Secure Boot est une fonctionnalité du BIOS/UEFI qui permet de s'assurer qu'un ordinateur démarre :
* en utilisant uniquement les logiciels approuvés par le fabricant de l'ordinateur, 
* n'ont pas été altérés depuis leur création, 

C'est pourquoi il est recommandé : 
* De le laisser activer et maintenir à jour le BIOS/UEFI pour maintenir à jour les signatures.
## PROTECTIONS
### AMD XD / INTEL NX
Intel et AMD ont tous deux pris des mesures pour tenter de fournir une meilleure protection contre les exploits grâce aux options Intel Execute Disable (XD) et AMD Enhanced Virus Protection (NX-bit).

Ces technologies permettent au système d'exploitation de marquer des pages de mémoire comme non exécutables. Cette capacité permet d'éviter certains débordements de tampon en empêchant l'exécution d'instructions sur les pages de mémoire marquées.

Bien que n'apportant pas une protection complète contre les logiciels malveillants, elle reste une couche supplémentaire de sécurité.

C'est pourquoi il est recommandé : 
* Activer cette option dès que possible.
### INTEL SGX
Les enclaves SGX sont des espaces de mémoire protégés et chiffrés, qui permettent aux applications de s'exécuter en mode sécurisé et isolé du reste du système d'exploitation. Les enclaves SGX peuvent être utilisées pour exécuter des opérations critiques tels que le stockage des mots de passe, les transactions financières, l'intelligence artificielle et l'apprentissage machine, ou d'autres tâches qui nécessitent une sécurité élevée.

C'est pourquoi il est recommandé : 
* Activer cette option dès que possible,
* Augmenter la mémoire allouée à cet espace (max 128Mb)
* S'assurer que vos développeurs prennent en compte cette spécificités dès que possible.
### TPM
Le Trusted Computing Group définit le TPM comme une puce informatique qui peut stocker en toute sécurité les artefacts utilisés pour authentifier la plate-forme.

Si une organisation envisage des mesures d'intégrité de démarrage avancées ou un chiffrement complet du disque, le TPM et les implications de son utilisation doivent être étudiés en détail. Le site Web du Trusted Computing Group fournit une grande quantité d'informations sur les applications supportées par le TPM.

Documentation : https://trustedcomputinggroup.org/work-groups/trusted-platform-module/ 

C'est pourquoi il est recommandé : 
* Activer TPM,
  * Activer TPM 2.0
* Vérifier que les équipements de sécurité logicielle utilise cette puce (exemle : Bitlocker, NAC)

#### TPM 2.0
TPM 2.0 permet de rendre la puce TPM visible pour le système d'exploitation.

Il y a plusieurs options possibles pour cette configuration :
* Clear : ce paramètre efface les informations du propriétaire du module TPM et le module TPM est activé après l'effacement.
* PPI Bypass for Enable Commands : Il s'agit d'une fonctionnalité qui permet de contourner l'interface utilisateur du BIOS pour activer les commandes TPM. Elle peut être utile pour automatiser les processus de sécurité sans intervention humaine.
* PPI Bypass for Disable Commands : Il s'agit d'une fonctionnalité qui permet de contourner l'interface utilisateur du BIOS pour désactiver les commandes TPM. Elle peut être utile pour empêcher les utilisateurs malveillants de désactiver la puce TPM.
* Key Storage Enable : Cette fonctionnalité permet de stocker des clés de chiffrement dans la puce TPM pour une sécurité accrue. Les clés peuvent être utilisées pour protéger des données sensibles, telles que des mots de passe, des fichiers cryptés, etc.
* Attestation Tenable : Cette fonctionnalité permet de vérifier l'intégrité du système en utilisant la puce TPM. Elle peut être utilisée pour garantir que le système n'a pas été compromis ou altéré de quelque manière que ce soit.
* SHA-256 : Il s'agit d'une fonction de hachage cryptographique qui est utilisée pour sécuriser les données en créant une empreinte numérique unique de chaque fichier ou document. Elle est utilisée dans les processus de sécurité tels que la signature numérique, la vérification de l'intégrité des fichiers, etc.
* PPI Bypass for Clear Commands : Il s'agit d'une fonctionnalité qui permet de contourner l'interface utilisateur du BIOS pour effacer les données stockées dans la puce TPM. Elle peut être utilisée pour effacer les données sensibles en cas de compromission ou de perte du système.

En sachant ça il est recommandé : 
* Désactiver toutes les options de bypass
* Activer SHA256 (ou plus si disponible)
### PERFORMANCE
Intel fournit plusieurs options dans sa partie performance.

* MultiCore support : ce champ indique si un seul cœur du processeur doivent être activé ou s'ils doivent tous l'être.
* Intel SpeedStep : ce champ indique si le mode Intel SpeetStep doit être activité ou non pour le processeur.
  * Intel SpeedStep est une technologie d'Intel qui permet de réduire la consommation d'énergie des processeurs en ajustant dynamiquement leur fréquence d'horloge en fonction de la charge de travail. Cela permet aux processeurs de fonctionner plus efficacement en économisant de l'énergie et en réduisant la chaleur générée.
* C states : cette option active ou désactive les états de veille additionnels du processeur.
  * Les C-states sont une fonctionnalité des processeurs modernes qui permet de réduire la consommation d'énergie en mettant en veille certaines parties du processeur lorsque celles-ci ne sont pas utilisées.
* Intel Turbo Boost : ce champ indique si le mode Intel TurboBoost doi être activé ou non sur le processeur.
  * Turbo Boost est une technologie d'Intel qui permet d'augmenter la fréquence d'horloge des processeurs lorsqu'ils fonctionnent en dessous de leur capacité maximale. Cela permet aux processeurs de fournir des performances supplémentaires lorsqu'elles sont nécessaires, tout en économisant de l'énergie lorsqu'elles ne le sont pas.
* HyperThread Control : ce champ indique si le mode HyperThreat doit être activé ou non.
  * Au lieu de donner une charge de travail importante à un seul cœur, les programmes threaded divisent le travail en plusieurs tâches (threads) logicielles. Ces tâches sont traitées en parallèle par différents cœurs de processeurs pour gagner du temps.

En sachant ça il est recommandé : 
* Activer le multicore,
* Activer Speedstep, C-States, Turboboost.
  * Ces derniers sont toutefois faillibles via des techniques d'attaque telles que la mesure de la consommation d'énergie pour extraire des informations sensibles de la puce, telles que des clés de chiffrement. Néanmoins ces attaques sont très sophistiqués et nécessitent un accès physique à l'ordinateur.
* Activer ou non l'HyperThread dépendra de votre ressenti. De nombreuses vulnérabilités sont découvertes dessus, les correctifs ont des impacts sur les performances de cette technologie et pour une part importantes des utilisateurs sa désactivation sera transparente.
## VIRTUALISATION
La technologie de virtualisation dans le BIOS permet également d'améliorer les performances des machines virtuelles en offrant un accès direct aux ressources matérielles, ce qui réduit les coûts de traitement des demandes d'accès des machines virtuelles.

C'est pourquoi il est recommandé : 
* Activer cette option uniquement sur les équipements de virtualisation
  * Dans le cas où celle-ci est activé, il est recommandé d’appliquer toutes les protections disponibles telles que SMM dès lors qu’elles n’ont pas d’impact sur la production.
## DEMARRAGE
### WAKE ON LAN
La fonction Wake-On-LAN est utilisée pour faire sortir un ordinateur de son état d'arrêt ou de veille afin de démarrer le système d'exploitation de l'ordinateur et de permettre l'activité du réseau.

Certains systèmes comme SCCM peuvent l'utiliser pour réveiller les ordinateur afin d'installer des correctifs aux heures définies.

Le Wake-on-LAN présente toutefois de nombreux risques : 
* Attaques par rebond : les attaquants peuvent utiliser le Wake-on-LAN pour faire rebondir leur attaque sur un ordinateur cible à travers un ordinateur tiers, ce qui rend l'origine de l'attaque difficile à localiser.
* Attaques de rejeu : les attaquants peuvent capturer des paquets de données envoyés pour réveiller un ordinateur et les rejouer plus tard pour réveiller l'ordinateur sans autorisation.
* Attaques de déni de service : les attaquants peuvent envoyer de nombreux paquets Wake-on-LAN à un ordinateur pour le réveiller en continu, ce qui peut entraîner une surcharge du réseau et une interruption des services.
* Attaques par injection de paquets : les attaquants peuvent injecter des paquets Wake-on-LAN malveillants qui peuvent exécuter du code malveillant sur l'ordinateur cible.
* Fuites d'informations : si les paquets Wake-on-LAN contiennent des informations sensibles, telles que des mots de passe, ils peuvent être interceptés et compromettre la sécurité de l'ordinateur cible.
* utilisation du broadcast : ces paquets peuvent être filtrés par les routeurs (qui limitent les domaines de diffusion) et peuvent empêcher le Wake-on-LAN de fonctionner de fonctionner correctement.

C'est pourquoi il est recommandé : 
* Désactiver cette option si aucun outil ne l'utilise
### WAKE ON TIMER
Lorsqu'un modèle d'ordinateur ne prend pas en charge la fonction Wake On LAN, cette fonction peut servir de mesure provisoire pour garantir que les systèmes de gestion des correctifs puissent contacter l'ordinateur de manière plus fiable en faisant démarrer le poste à un horaire défini.

C'est pourquoi il est recommandé : 
* Désactiver cette option si aucun outil ne l'utilise
## COMMUNICATIONS RESEAU
### COMMUTATION SANS FIL
La commutation sans fil (wireless switching) empêche le raccordement du réseau au niveau matériel. Une fois que le lien est détecté sur l'adaptateur réseau câblé, l'adaptateur sans fil est automatiquement désactivé.

Cette fonction est une excellente mesure pour :
* Empêcher les ponts de réseau non autorisés : le pontage de réseau est une technique utilisée pour contourner les contrôles de sécurité en établissant une connexion entre deux réseaux différents. La désactivation de la connectivité sans fil peut empêcher les utilisateurs d'établir des ponts de réseau non autorisés en désactivant automatiquement la connexion sans fil lorsque la connexion filaire est détectée.
* Limiter les risques de compromission de la sécurité du réseau en empêchant les utilisateurs d'établir des ponts de réseau non autorisés, la désactivation de la connectivité sans fil peut contribuer à limiter les risques de compromission de la sécurité du réseau en empêchant les attaquants potentiels d'accéder aux données sensibles ou de lancer des attaques de type "man-in-the-middle" à partir de leur propre machine.

C'est pourquoi il est recommandé : 
* Activer cette option
### CONTROLEUR D'INTERFACE RESEAU (NIC)
Dans les BIOS des ordinateurs Dell, la fonctionnalité "Integrated NIC" permet de contrôler les paramètres de la carte réseau intégrée. Par exemple, vous pouvez activer ou désactiver la carte réseau, configurer les paramètres de vitesse et de duplex (par exemple, 10/100/1000 Mbps), ou configurer des paramètres avancés tels que la prise en charge de VLAN ou le Wake-on-LAN.

C'est pourquoi il est recommandé : 
* Activer cette option uniquement si le poste doit utiliser la carte réseau intégrée,
  * Désactiver le boot PXE si inutile
  * Activer le boot PXE si utilisé
    * Activer les protections disponibles avec l'UEFI sur ce protocole
## CONFIGURATION EQUIPEMENTS
### SATA OPERATION
Cette option configure le mode de fonctionnement du contrôleur de disque dur SATA intégré.

* Disabled : les ports SATA sont désactivés et aucun périphérique SATA ne peut être utilisé. Cette option est généralement utilisée si vous ne prévoyez pas d'utiliser de périphérique SATA.
* AHCI : le contrôleur SATA est configuré en mode AHCI (Advanced Host Controller Interface). Le mode AHCI est généralement recommandé pour les disques durs et les SSD modernes car il permet une meilleure performance et une plus grande flexibilité dans la gestion des disques. Il prend également en charge les fonctionnalités avancées telles que le NCQ (Native Command Queuing), la gestion de l'alimentation et la hot-swapping.
* RAID On : le contrôleur SATA est configuré pour prendre en charge les configurations de disques RAID. Cette option est généralement utilisée si vous prévoyez de configurer un système RAID à l'aide de deux ou plusieurs disques durs ou SSD. Le mode RAID permet de créer des configurations de disques redondantes pour améliorer la tolérance aux pannes et les performances de stockage.

C'est pourquoi il est recommandé : 
* Activer le mode AHCI pour la majorité du parc
* Activer le mode RAID uniquement si le poste est configuré pour avoir du RAID.
### USB
Ce champ configure le contrôleur USB interactif.

Lorsque l'USB interactif est activé, cela signifie que les ports USB de l'ordinateur peuvent être utilisés pour interagir avec le BIOS, par exemple pour effectuer une mise à jour du BIOS à partir d'une clé USB ou pour démarrer à partir d'un périphérique de stockage USB.

_Il est important de noter que les claviers et souris seront toujours fonctionnels dans le BIOS indépendamment de la configuration._

La partie "USB Configuration" dans les BIOS Dell permet de configurer les paramètres liés aux ports USB du système. Les options les plus courantes sont "Enable USB Boot support" et "Enable External USB port". Voici les rôles de ces deux options :

* Enable USB Boot support : permet de démarrer le système à partir d'un périphérique USB tel qu'une clé USB ou un disque dur externe. Si cette option est désactivée, le système ne pourra pas être démarré à partir d'un périphérique USB.
* Enable External USB port : permet ou non l'utilisation des ports USB externes sur le système. Si cette option est désactivée, les ports USB externes ne fonctionneront pas. Cela peut être utile pour des raisons de sécurité si l'on souhaite restreindre l'accès aux ports USB externes pour empêcher les utilisateurs non autorisés de connecter des périphériques USB

C'est pourquoi il est recommandé : 
* Désactiver la possibilité de démarrer sur clef USB,
* Désactiver l'utilisation des ports USB externes si non utilisés (par exemple si non utilisation de docking station).
## HEALTHCHECK
### SMART
Le test SMART effectue des vérifications en arrière-plan pour détecter les signes avant-coureurs de défaillance imminente du disque dur, tels que des secteurs défectueux, des taux d'erreurs élevés, des températures anormalement élevées, etc. 
Si des problèmes sont détectés, le test SMART peut avertir l'utilisateur de la nécessité de remplacer le disque dur avant qu'une défaillance complète ne se produise, ce qui permet de sauvegarder les données et d'éviter les pertes de données.

Bien que ce ne soit pas purement de la sécurité, il est recommandé d'activer cette option.
## MISE A JOUR ET DOWNGRADE
### PROCEDURE UPDATE / DOWNGRADE
Le BIOS étant le programme gérant le démarrage du poste il est la première brique à consolider.
Les mises à jour peuvent comprendre : 
* Correction de bugs et de vulnérabilités de sécurité
* Amélioration des fonctionnalités
* Optimisation des performances
* Résolution de problèmes de compatibilité

C'est pourquoi il est recommandé de : 
* Mettre en place un processus de qualification de chaque mise à jour du BIOS,
* Mettre en place une politique de déploiement,
* Auditer le déploiement de cette mise à jour.
### UEFI ENCAPSULATION POUR MISE A JOUR DE FIRMWARE
Ceci permet à Windows Update de fournir les mises à jour du BIOS.

En fonction de votre politique de mise à jour, il faudra définir si vous activez ou non cette option.
Attention, de récents posts sur les forums indique que Windows 11 ne tient pas compte de cette option.

## COMPUTRACE
Computrace est utilisé par des fournir des services tels que : 
* Sécuriser les données d’un parc de postes à distance,
* Déployer toujours à distance des mises à jour, des licences ou de lancer des audits,
* Géolocaliser des ordinateurs volés,
* Produire des rapports concernant les machines,
* Récupérer des fichiers,
* Effacer à distance des documents ou tout le disque dur.

Cette fonctionnalité est très puissante. Un attaquant pourrait s'en servir pour déployer un moyen de persistance ou une porte dérobée au sein du système d’information. Porte qui ne pourrait être supprimée même en réinstaller, formatant ou changeant le disque ou le BIOS.
Ajoutons a cela que la fonctionnalité semble très légère en terme de sécurité (injection de code, protocole pas chiffré, pas d'authentifiation, appel via simple URL).

Documentation : https://www.kaspersky.com/blog/beware-of-vulnerable-anti-theft-applications/3837/  

C'est pourquoi il est recommandé de : 
* Désactiver cette fonctionnalité.