# Rapport d'audit de sécurité - Application Android

## Informations générales
- **Application** : VulnerableApp
- **Package** : com.example.vulnerableapp
- **Version** : 1.0
- **Date d'audit** : 2026-05-03
- **Auditeur** : Daber Ziyad

## Résumé exécutif
L'audit de sécurité réalisé sur l'application **VulnerableApp** a révélé plusieurs vulnérabilités critiques liées à la mauvaise configuration des composants Android. L'exposition non protégée d'activités, de services et de fournisseurs de contenu (Content Providers) permettrait à un attaquant de contourner l'authentification, d'accéder à des données utilisateurs sensibles et d'exécuter des fonctionnalités privilégiées sans autorisation. La priorité absolue doit être accordée à la sécurisation du `UserDataProvider` et de la `LoginActivity`.

## Méthodologie
- **Analyse statique** : Examen du fichier `AndroidManifest.xml` pour identifier les composants exportés.
- **Cartographie dynamique** : Utilisation de l'outil **Drozer** pour lister et analyser les composants exposés.
- **Vérification des protections** : Analyse des permissions et des intent-filters associés à chaque composant.
- **Analyse des risques** : Évaluation de l'impact potentiel basé sur les standards de sécurité mobiles.

## Découvertes principales
1. **Fuite de données critiques (V2)** : Le `UserDataProvider` est exporté et accessible sans permission, permettant l'extraction massive de données utilisateurs.
2. **Contournement d'authentification (V1)** : La `LoginActivity` est exportée sans protection, permettant potentiellement l'accès direct à des écrans internes.
3. **Accès non autorisé aux données (V5)** : `UserProfileActivity` utilise des permissions trop faibles, rendant la protection inefficace.
4. **Exécution de services non autorisée (V3)** : `DataSyncService` est exporté sans validation d'intent, permettant le déclenchement de synchronisations malveillantes.

## Recommandations prioritaires
- **Désactiver l'exportation** : Passer `android:exported="false"` pour tous les composants qui ne doivent pas être accessibles par d'autres applications.
- **Implémenter des permissions de signature** : Pour les composants devant rester exportés, utiliser des permissions avec `android:protectionLevel="signature"`.
- **Valider les Intents** : Ajouter des vérifications rigoureuses de l'origine et du contenu des intents dans les services et broadcast receivers.
- **Sécuriser les Content Providers** : Restreindre l'accès aux URI sensibles via des permissions de lecture/écriture strictes.

## Annexes

### Annexe A : Tableau de triage complet
| ID | Composant | Vulnérabilité | Confiance | Sévérité | Impact | Recommandation | Statut |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| V1 | LoginActivity | Exportée sans protection | Élevée | Élevée | Contournement d'authentification | Définir exported=false | À corriger |
| V2 | UserDataProvider | URI accessibles sans permission | Élevée | Critique | Fuite de données utilisateur | Ajouter permission de lecture/écriture | À corriger |
| V3 | DataSyncService | Service exporté sans validation | Moyenne | Moyenne | Exécution de synchronisation non autorisée | Implémenter validation d'intent | À corriger |
| V4 | BootReceiver | Receiver exporté sans validation | Élevée | Faible | Déclenchement au démarrage | Ajouter validation d'intent | À surveiller |
| V5 | UserProfileActivity | Protection par permission faible | Élevée | Moyenne | Accès à des données utilisateur | Utiliser permission de niveau supérieur | À corriger |

### Annexe B : Mapping OWASP MASVS
| ID | Vulnérabilité | Référence MASVS | Description |
| :--- | :--- | :--- | :--- |
| V1 | Activities exportées sans protection | MSTG-PLATFORM-1 | L'application doit n'exposer que les composants nécessaires |
| V2 | Content Providers mal protégés | MSTG-STORAGE-2 | Aucune donnée sensible ne doit être stockée sans protection adéquate |
| V3 | Services exportés sans validation | MSTG-PLATFORM-2 | Les entrées des sources externes doivent être validées |
| V4 | Broadcast Receivers sans validation | MSTG-PLATFORM-3 | L'application doit valider les intents reçus |
| V5 | Permissions insuffisantes | MSTG-AUTH-1 | Les mécanismes d'authentification doivent être robustes |
