# Procédure d'installation de LAPS (Local Administrator Password Solution)

## Pré-requis
- Serveur Active Directory (AD) en cours de fonctionnement.
- Un compte d'administrateur avec des privilèges suffisants pour effectuer des modifications sur le schéma AD.
- Téléchargement du fichier **LAPS** depuis le [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=46899).

## Étape 1 : Téléchargement et installation de LAPS

1. **Accéder à la page de téléchargement :**
   - Rendez-vous sur la page de téléchargement du Local Administrator Password Solution (LAPS).
   
   ![Page de téléchargement de LAPS](cap1_laps_download.PNG)

2. **Choisir la version appropriée :**
   - Sélectionnez le fichier d'installation correspondant à votre architecture système :
     - **LAPS.x64.msi** pour les systèmes 64 bits.
     - **LAPS.x86.msi** pour les systèmes 32 bits.
   
   ![Choix du fichier LAPS](cap2_laps_choicex64.PNG)

3. **Lancer l'installation :**
   - Une fois le fichier téléchargé, double-cliquez sur le fichier `LAPS.x64.msi`.
   
   ![Démarrer l'installation](cap3_launchmsi.PNG)

4. **Suivre l'assistant d'installation :**
   - Cliquez sur **Next** à l'écran de bienvenue.
   
   ![Écran de bienvenue](cap4_alaunch.PNG)

5. **Sélectionner les composants :**
   - Choisissez les options suivantes :
     - **AdmPwd GPO Extension**.
     - **Management Tools** (y compris Fat client UI, PowerShell module et GPO Editor templates).
   
   ![Sélection des options](cap5_blaunchchoice.PNG)
   
   - Assurez-vous que toutes les sous-catégories sont sélectionnées.
   
   ![Options complètes](cap6_claunchchoice.PNG)

6. **Lancer l'installation :**
   - Cliquez sur **Install** pour commencer l'installation de LAPS.
   
   ![Prêt à installer](cap7_endinstall.PNG)

7. **Finaliser l'installation :**
   - Cliquez sur **Finish** à la fin du processus.
   
   ![Installation terminée](cap8_finish.PNG)

## Étape 2 : Configuration de LAPS dans Active Directory

1. **Importer le module PowerShell LAPS :**
   - Ouvrez une fenêtre PowerShell en tant qu'administrateur.
   
   ![PowerShell](cap9_cmdPWSL.PNG)
   
   - Tapez la commande suivante :
     ```powershell
     Import-Module AdmPwd.PS
     ```
   
   ![Importation du module](cap10_cmdPWSL.PNG)

2. **Mettre à jour le schéma AD :**
   - Exécutez la commande suivante pour mettre à jour le schéma avec les attributs de LAPS :
     ```powershell
     Update-AdmPwdADSchema
     ```
   
   ![Mise à jour du schéma](cap11_cmdPWSL.PNG)
   
   - Vous devriez voir le message **Success** sur les modifications d'attributs.

3. **Configurer les autorisations :**
   - Donnez les permissions de mise à jour des mots de passe pour les ordinateurs :
     ```powershell
     Set-AdmPwdComputerSelfPermission -OrgUnit "OU=Computers,DC=pharmgreen,DC=com"
     ```
   
   ![Permissions ordinateur](cap12_cmdPWSLauth0.PNG)

   - Si vous avez plusieurs unités d'organisation (OU), vous pouvez les spécifier dans la commande avec leur chemin complet :
     ```powershell
     Set-AdmPwdComputerSelfPermission -OrgUnit "OU=PG_Computers,OU=PG_Lyon,OU=PG_France,DC=pharmgreen,DC=com"
     ```
   
   ![Autorisations multiples](cap13_cmdPWSLOUPGCOMP.PNG)

4. **Délégation des autorisations :**
   - Attribuez les permissions de lecture du mot de passe local à un groupe spécifique (par exemple, **Domain Admins**):
     ```powershell
     Set-AdmPwdReadPasswordPermission -OrgUnit "OU=PG_Computers,OU=PG_Lyon,OU=PG_France,DC=pharmgreen,DC=com" -AllowedPrincipals "Domain Admins"
     ```
   
   ![Lecture des mots de passe](cap14_cmdPWSLOUPGCOMP.PNG)

   - Attribuez les permissions de réinitialisation du mot de passe :
     ```powershell
     Set-AdmPwdResetPasswordPermission -OrgUnit "OU=PG_Computers,OU=PG_Lyon,OU=PG_France,DC=pharmgreen,DC=com" -AllowedPrincipals "Domain Admins"
     ```
   
   ![Réinitialisation des mots de passe](cap15_cmdPWSLOUPGCOMP.PNG)

## Étape 3 : Configuration de la stratégie de groupe (GPO)

1. **Créer une nouvelle stratégie GPO :**
   - Accédez au gestionnaire de stratégie de groupe et créez une nouvelle stratégie nommée **LAPS Configuration**.

   ![Création GPO](capGPO1_creation.PNG)

2. **Configurer les paramètres de LAPS dans la GPO :**
   - Allez dans **Computer Configuration** -> **Policies** -> **Administrative Templates** -> **LAPS**.
   - Définissez les paramètres suivants :
     - **Enable local admin password management** : Enabled.
     - **Password Complexity** : High.
     - **Password Length** : 14 caractères minimum.
   
   ![Paramètres de mot de passe](capGPO3_gpopoliciespasswordsettings.PNG)

3. **Activer la GPO :**
   - Liez la GPO à l'Unité Organisationnelle (OU) contenant les ordinateurs sur lesquels LAPS sera appliqué.
   
   ![Activation de la GPO](capGPO4_enableGPO.PNG)

   - Vérifiez que la GPO est correctement appliquée à l'OU.

## Résolution des erreurs courantes

- **Erreur de permissions lors de la configuration :**
  - Si vous rencontrez des erreurs telles que `The object does not exist`, vérifiez que l'OU spécifiée existe dans Active Directory.

  ![Erreur de permissions](erreur1.PNG)

- **Vérification des OUs :**
  - Assurez-vous que les unités d'organisation sont correctement nommées et accessibles via le gestionnaire d'Active Directory.
  
  ![Recherche des OUs](searchoucomputer.PNG)

## Conclusion
L'installation et la configuration de LAPS sont maintenant terminées. Vous pouvez vérifier le bon fonctionnement en essayant de récupérer les mots de passe locaux des ordinateurs membres du domaine via PowerShell ou l'interface utilisateur de LAPS.
