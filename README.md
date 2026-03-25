# developementmobile-lab22x    

# Objectifs pédagogiques  

À la fin de ce laboratoire, il sera possible de :  

créer un projet Android avec support C++ ;  

comprendre le rôle du NDK, de CMake et de JNI ;  

déclarer et appeler des méthodes natives depuis Java ;  

manipuler des types simples et complexes entre Java et C++ ;  

gérer des erreurs fréquentes comme UnsatisfiedLinkError ;  

lire les logs natifs dans Logcat ;  

appliquer de bonnes pratiques récentes pour JNI, optimisation et sécurité.  


## Étape 1 — Créer le projet  

<img width="1165" height="830" alt="image" src="https://github.com/user-attachments/assets/e7d0b6f4-e4a8-4b15-8c4e-5991173d37c6" />    

# Prérequis  

 
 Android Studio installé ;  
 
SDK Android configuré ;  

NDK, CMake et LLDB disponibles dans le SDK Manager ;  

connaissances de base sur Activity, layout XML et Java.  
<img width="1112" height="369" alt="image" src="https://github.com/user-attachments/assets/7e69733e-a3ef-43dc-955c-40082f10cb69" />    

# Avant de coder, il est important de distinguer les briques :  

JNI  

JNI est l’interface qui permet au code Java/Kotlin d’appeler du code natif C/C++. Android précise que JNI permet au bytecode Android d’interagir avec des bibliothèques partagées dynamiques en natif.  

NDK  

Le NDK est l’ensemble d’outils qui permet d’utiliser C/C++ dans Android. Il donne accès à la compilation native et à différentes API natives.
CMake  

CMake est l’outil de build utilisé pour décrire comment compiler et lier la bibliothèque native. Android recommande CMake ou ndk-build comme solutions officielles de construction.  

Bibliothèque partagée .so  

Le code natif Android est généralement empaqueté dans une bibliothèque partagée,   
par exemple  
libnative-lib.so, chargée via System.loadLibrary("native-lib")

