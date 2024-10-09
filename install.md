# Procédure d'installation de LAPS (Local Administrator Password Solution)

## Pré-requis
- Serveur Active Directory (AD) en cours de fonctionnement.
- Un compte d'administrateur avec des privilèges suffisants pour effectuer des modifications sur le schéma AD.
- Téléchargement du fichier **LAPS** depuis le [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=46899).

## Étape 1 : Téléchargement et installation de LAPS

1. **Accéder à la page de téléchargement :**
   - Rendez-vous sur la page de téléchargement du Local Administrator Password Solution (LAPS).
   
   ![![cap1_laps_download](https://github.com/user-attachments/assets/654e2ad4-24e0-409a-843f-efc6df067b19)

2. **Choisir la version appropriée :**
   - Sélectionnez le fichier d'installation correspondant à votre architecture système :
     - **LAPS.x64.msi** pour les systèmes 64 bits.
     - **LAPS.x86.msi** pour les systèmes 32 bits.
   
  ![cap2_laps_choicex64](https://github.com/user-attachments/assets/ebb105f2-8ba5-4ec3-8637-e5e751319656)


3. **Lancer l'installation :**
   - Une fois le fichier téléchargé, double-cliquez sur le fichier `LAPS.x64.msi`.
   
   !![cap3_launchmsi](https://github.com/user-attachments/assets/3e2dfda4-cd1e-4193-af0a-696d9be169ef)
[
4. **Suivre l'assistant d'installation :**
   - Cliquez sur **Next** à l'écran de bienvenue.
   
   ![É![cap4_alaunch](https://github.com/user-attachments/assets/3a66824a-6a78-4888-b468-bcbfd0f8508c)

5. **Sélectionner les composants :**
   - Choisissez les options suivantes :
     - **AdmPwd GPO Extension**.
     - **Management Tools** (y compris Fat client UI, PowerShell module et GPO Editor templates).
   
  ![cap5_blaunchchoice](https://github.com/user-attachments/assets/54ac8d1c-09c8-4199-93bf-d8f6689034b6)
   
   - Assurez-vous que toutes les sous-catégories sont sélectionnées.
   
   ![cap6_claunchchoice](https://github.com/user-attachments/assets/6a7d0f02-b471-437f-8c89-ee8924b8a7b1)

6. **Lancer l'installation :**
   - Cliquez sur **Install** pour commencer l'installation de LAPS.
   
   !![cap7_endinstall](https://github.com/user-attachments/assets/71f00381-9a64-4d14-91bc-1dda20ce2cff)

7. **Finaliser l'installation :**
   - Cliquez sur **Finish** à la fin du processus.
   
   !![cap8_finish](https://github.com/user-attachments/assets/289a0c7f-b541-4214-960d-a094688835b3)

## Étape 2 : Configuration de LAPS dans Active Directory

1. **Importer le module PowerShell LAPS :**
   - Ouvrez une fenêtre PowerShell en tant qu'administrateur.
   
![cap9_cmdPWSL](https://github.com/user-attachments/assets/e60a846e-babb-410b-bf5d-702cfefb1a2b)

   
   - Tapez la commande suivante :
     ```powershell
     Import-Module AdmPwd.PS
     ```
     
   ![cap10_cmdPWSL](https://github.com/user-attachments/assets/19c54d65-63c6-40dd-a610-ac48239ef737)

   
3. **Mettre à jour le schéma AD :**
   - Exécutez la commande suivante pour mettre à jour le schéma avec les attributs de LAPS :
     ```powershell
     Update-AdmPwdADSchema
     ```
     
   ![cap11_cmdPWSL](https://github.com/user-attachments/assets/6ab59a58-5f2f-4904-9b73-efb63ddfe883)

  
   - Vous devriez voir le message **Success** sur les modifications d'attributs.

4. **Configurer les autorisations :**
   - Donnez les permissions de mise à jour des mots de passe pour les ordinateurs :
     ```powershell
     Set-AdmPwdComputerSelfPermission -OrgUnit "OU=Computers,DC=pharmgreen,DC=com"
     ```

   ![cap12_cmdPWSLauth0](https://github.com/user-attachments/assets/f7a1957b-e47a-40e2-bd3d-967ad21dcc93)

  
   - Si vous avez plusieurs unités d'organisation (OU), vous pouvez les spécifier dans la commande avec leur chemin complet :
     ```powershell
     Set-AdmPwdComputerSelfPermission -OrgUnit "OU=PG_Computers,OU=PG_Lyon,OU=PG_France,DC=pharmgreen,DC=com"
     ```
     
   ![cap13_cmdPWSLOUPGCOMP](https://github.com/user-attachments/assets/42065b7f-0c03-4811-b067-b27a15bafcdc)

  
5. **Délégation des autorisations :**
   - Attribuez les permissions de lecture du mot de passe local à un groupe spécifique (par exemple, **Domain Admins**):
     ```powershell
     Set-AdmPwdReadPasswordPermission -OrgUnit "OU=PG_Computers,OU=PG_Lyon,OU=PG_France,DC=pharmgreen,DC=com" -AllowedPrincipals "Domain Admins"
     ```
     
   ![cap14_cmdPWSLOUPGCOMP](https://github.com/user-attachments/assets/c55e24dc-d49c-4dae-8008-d4d1628a00d2)

  
   - Attribuez les permissions de réinitialisation du mot de passe :
     ```powershell
     Set-AdmPwdResetPasswordPermission -OrgUnit "OU=PG_Computers,OU=PG_Lyon,OU=PG_France,DC=pharmgreen,DC=com" -AllowedPrincipals "Domain Admins"
     ```
     
   ![cap15_cmdPWSLOUPGCOMP](https://github.com/user-attachments/assets/a06f3ed3-5473-4087-a004-dc36e3eeaaeb)

  
## Étape 3 : Configuration de la stratégie de groupe (GPO)

1. **Créer une nouvelle stratégie GPO :**
   - Accédez au gestionnaire de stratégie de groupe et créez une nouvelle stratégie nommée **LAPS Configuration**.

![capGPO1_creation](https://github.com/user-attachments/assets/868ab6ba-2c32-4f04-927d-a0a797b33cc7)

    
3. **Configurer les paramètres de LAPS dans la GPO :**
   - Allez dans **Computer Configuration** -> **Policies** -> **Administrative Templates** -> **LAPS**.
   - Définissez les paramètres suivants :
     - **Enable local admin password management** : Enabled.
     - **Password Complexity** : High.
     - **Password Length** : 14 caractères minimum.
   
   ![capGPO3_gpopoliciespasswordsettings](https://github.com/user-attachments/assets/10f886b5-45cc-4479-8bdb-ca967d3510c6)

4. **Activer la GPO :**
   - Liez la GPO à l'Unité Organisationnelle (OU) contenant les ordinateurs sur lesquels LAPS sera appliqué.
   
![capGPO4_enableGPO](https://github.com/user-attachments/assets/59299abe-4082-4756-b972-1072ef90b9c4)

   
   - Vérifiez que la GPO est correctement appliquée à l'OU.

## Résolution des erreurs courantes

- **Erreur de permissions lors de la configuration :**
  - Si vous rencontrez des erreurs telles que `The object does not exist`, vérifiez que l'OU spécifiée existe dans Active Directory.

 ![erreur1](https://github.com/user-attachments/assets/f54e4dc4-f377-4974-ba16-58ab68c6e928)

 
- **Vérification des OUs :**
  - Assurez-vous que les unités d'organisation sont correctement nommées et accessibles via le gestionnaire d'Active Directory.
  - 
  ![searchoucomputer](https://github.com/user-attachments/assets/e6fa2dd3-00ba-4ce3-8955-d68c703c4d57)


## Conclusion
L'installation et la configuration de LAPS sont maintenant terminées. Vous pouvez vérifier le bon fonctionnement en essayant de récupérer les mots de passe locaux des ordinateurs membres du domaine via PowerShell ou l'interface utilisateur de LAPS.
