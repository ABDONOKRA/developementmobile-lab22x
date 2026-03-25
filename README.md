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

<img width="1057" height="832" alt="image" src="https://github.com/user-attachments/assets/199bc16e-7fa8-49d5-b5c1-922b828f2881" />


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

# Étape 4 — Configurer le fichier CMakeLists.txt    

``` app/src/main/cpp/CMakeLists.txt ```

  <img width="1240" height="556" alt="image" src="https://github.com/user-attachments/assets/117f2307-6b51-4017-bf13-636a747fbe89" />

  # Étape 5 — Écrire le code natif C++    
  
  # JNI Demo Lab

## Fichiers du projet
```
app/src/main/java/com/example/jnidemo/MainActivity.java
app/src/main/cpp/native-lib.cpp
app/src/main/cpp/CMakeLists.txt
```

## native-lib.cpp

```cpp
#include <jni.h>
#include <algorithm>
#include <climits>
#include <android/log.h>

#define LOGI(...) __android_log_print(ANDROID_LOG_INFO, "NativeLib", __VA_ARGS__)
#define LOGE(...) __android_log_print(ANDROID_LOG_ERROR, "NativeLib", __VA_ARGS__)

extern "C" {

JNIEXPORT jstring JNICALL
Java_com_example_jnidemo_MainActivity_helloFromJNI(JNIEnv *env, jobject /* this */) {
    LOGI("helloFromJNI called");
    return env->NewStringUTF("Hello from C++!");
}

JNIEXPORT jint JNICALL
Java_com_example_jnidemo_MainActivity_factorial(JNIEnv *env, jobject /* this */, jint n) {
    LOGI("factorial called with n = %d", n);
    
    if (n < 0) return -1;
    
    jint result = 1;
    for (jint i = 2; i <= n; i++) {
        if (result > INT_MAX / i) return -2;
        result *= i;
    }
    return result;
}

JNIEXPORT jstring JNICALL
Java_com_example_jnidemo_MainActivity_reverseString(JNIEnv *env, jobject /* this */, jstring str) {
    const char *input = env->GetStringUTFChars(str, nullptr);
    if (input == nullptr) return nullptr;
    
    std::string cppStr(input);
    std::reverse(cppStr.begin(), cppStr.end());
    
    env->ReleaseStringUTFChars(str, input);
    return env->NewStringUTF(cppStr.c_str());
}

JNIEXPORT jint JNICALL
Java_com_example_jnidemo_MainActivity_sumArray(JNIEnv *env, jobject /* this */, jintArray arr) {
    jint *elements = env->GetIntArrayElements(arr, nullptr);
    if (elements == nullptr) return 0;
    
    jsize length = env->GetArrayLength(arr);
    jint sum = 0;
    
    for (jsize i = 0; i < length; i++) {
        sum += elements[i];
    }
    
    env->ReleaseIntArrayElements(arr, elements, JNI_ABORT);
    return sum;
}

}
```
## Explication du code C++

### Les includes et macros

```cpp
#include <jni.h>          // Types JNI (jstring, jint, etc.)
#include <algorithm>       // Pour std::reverse
#include <climits>         // Pour INT_MAX
#include <android/log.h>   // Pour écrire dans Logcat
```

# Étape 6 — Déclarer les méthodes natives côté Java  
<img width="1296" height="981" alt="image" src="https://github.com/user-attachments/assets/83c2fe78-709d-408b-9a66-9ee6670e90d1" />      
-> Les 4 méthodes native sont déclarées ✅  

-> Le bloc static charge bien "native-lib" ✅  

-> Les 4 TextView sont appelés ✅  


# Étape 7 — Créer le layout XML    
<img width="1296" height="981" alt="image" src="https://github.com/user-attachments/assets/6af5567b-e48a-423a-bfd5-a726e3b98e8c" />  

-> Les 4 TextView ont bien les ids : tvHello, tvFact, tvReverse, tvArray ✅  

-> Les erreurs rouges dans MainActivity.java ont disparu ✅    
# Étape 8 — Compiler et exécuter     

<img width="594" height="958" alt="image" src="https://github.com/user-attachments/assets/9ef24b53-6a3e-4cfa-8b0b-b45bb9b486c5" />      

##  Résultats des tests JNI

Voici le récapitulatif des opérations effectuées via l'interface native (C++) :

| Résultat | Status |
| :--- | :---: |
| `Hello from C++ via JNI !` | ✅ |
| `Factoriel de 10 = 3628800` | ✅ |
| `Texte inverse : !lufrewop si JNI` | ✅ |
| `Somme du tableau = 150` | ✅ |  

# Étape 9 — Vérifier les logs natifs dans Logcat

































