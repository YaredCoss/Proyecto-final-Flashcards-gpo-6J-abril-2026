Para desarrollar este ecosistema basado en el estándar de agentes y la infraestructura de Flutter/Firebase, estructuraremos la respuesta en dos partes: primero, la definición de la habilidad global `.agents` y, segundo, la implementación técnica del proyecto **proyectoflashcards**.

---

## 1. Definición de la Habilidad Agente Global `.agents`

Esta estructura permite que cualquier IDE compatible (VS Code o Antigravity) reconozca las capacidades del agente para asistir en el desarrollo.

### Estructura de Carpetas
```text
.agents/
├── SKILL.md
├── scripts/
│   ├── check_env.sh      # Verifica Flutter/Firebase
│   └── deploy_db.sh      # Configura reglas de Firestore
├── ejemplos/
│   └── deck_model_example.json
└── resources/
    └── firebase_config_guide.md
```

### Contenido de `SKILL.md`
Este archivo define la identidad del agente.

```markdown
# Skill: Desarrollador Flashcards Pro
**Descripción:** Experto en arquitectura Flutter y persistencia en Firebase Firestore.

## Capacidades
- **Diseño UI:** Creación de interfaces limpias para aprendizaje.
- **Código:** Generación de lógica CRUD en Dart.
- **Scraping:** (Opcional) Importación de datos para mazos.

## Flujo de Trabajo
1. Verificación de entorno.
2. Configuración de Firebase.
3. Generación de Modelos y Pantallas.
```

---

## 2. Preparación del Entorno y Prerrequisitos

Antes de codificar, debemos asegurar que las herramientas estén listas.

### Verificación de Herramientas
Ejecuta en tu terminal:
1. **Flutter:** `flutter --version` (Si no está, descárgalo en flutter.dev).
2. **Firebase CLI:** `npm install -g firebase-tools`.
3. **Login:** `firebase login`.
4. **FlutterFire:** `dart pub global activate flutterfire_cli`.

### Configuración del Proyecto Firebase
1. Ve a [Firebase Console](https://console.firebase.google.com/).
2. Crea un proyecto llamado `proyectoflashcards`.
3. Habilita **Cloud Firestore** en modo prueba.
4. Vincula tu app ejecutando: `flutterfire configure`.

---

## 3. Implementación: Proyecto Flashcards

### Estructura de Archivos (proyectoflashcards)
```text
lib/
├── models/
│   └── mazo_model.dart
├── services/
│   └── firebase_service.dart
├── screens/
│   ├── home_screen.dart
│   └── mazo_form_screen.dart
└── main.dart
```

### `pubspec.yaml` (Dependencias)
Asegúrate de incluir:
```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.0.0
  cloud_firestore: ^4.0.0
```

### Código: Modelo de Datos (`lib/models/mazo_model.dart`)
```dart
class Mazo {
  String id;
  String nombre;
  String descripcion;

  Mazo({required this.id, required this.nombre, required this.descripcion});

  Map<String, dynamic> toMap() => {
    "nombre": nombre,
    "descripcion": descripcion,
  };

  factory Mazo.fromFirestore(Map<String, dynamic> data, String id) {
    return Mazo(
      id: id,
      nombre: data['nombre'] ?? '',
      descripcion: data['descripcion'] ?? '',
    );
  }
}
```

### Código: Servicio CRUD (`lib/services/firebase_service.dart`)
```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/mazo_model.dart';

class FirebaseService {
  final CollectionReference mazosRef = FirebaseFirestore.instance.collection('mazos');

  // Create
  Future<void> addMazo(String nombre, String desc) {
    return mazosRef.add({'nombre': nombre, 'descripcion': desc});
  }

  // Read (Stream)
  Stream<List<Mazo>> getMazos() {
    return mazosRef.snapshots().map((snapshot) =>
        snapshot.docs.map((doc) => Mazo.fromFirestore(doc.data() as Map<String, dynamic>, doc.id)).toList());
  }

  // Update
  Future<void> updateMazo(String id, String nombre, String desc) {
    return mazosRef.doc(id).update({'nombre': nombre, 'descripcion': desc});
  }

  // Delete
  Future<void> deleteMazo(String id) {
    return mazosRef.doc(id).delete();
  }
}
```

### Código: UI Principal (`lib/screens/home_screen.dart`)
```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';
import 'mazo_form_screen.dart';

class HomeScreen extends StatelessWidget {
  final FirebaseService _service = FirebaseService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Mis Mazos Flashcards")),
      body: StreamBuilder(
        stream: _service.getMazos(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          var mazos = snapshot.data!;
          return ListView.builder(
            itemCount: mazos.length,
            itemBuilder: (context, index) => ListTile(
              title: Text(mazos[index].nombre),
              subtitle: Text(mazos[index].descripcion),
              trailing: Row(
                mainAxisSize: MainAxisSize.min,
                children: [
                  IconButton(icon: Icon(Icons.edit), onPressed: () => Navigator.push(context, MaterialPageRoute(builder: (_) => MazoFormScreen(mazo: mazos[index])))),
                  IconButton(icon: Icon(Icons.delete, color: Colors.red), onPressed: () => _service.deleteMazo(mazos[index].id)),
                ],
              ),
            ),
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () => Navigator.push(context, MaterialPageRoute(builder: (_) => MazoFormScreen())),
      ),
    );
  }
}
```

---

## 4. Pruebas de Verificación de Funcionalidad

Para asegurar que el proyecto es totalmente funcional, realiza este checklist:

1.  **Conexión:** Ejecuta `flutter run`. Si la pantalla carga en blanco, verifica que `Firebase.initializeApp()` esté en tu `main.dart`.
2.  **Escritura:** Usa el botón "+" para crear un mazo. Verifica que aparezca en la consola de Firebase.
3.  **Lectura:** Los datos deben aparecer automáticamente en la lista gracias al `StreamBuilder`.
4.  **Eliminación:** Borra un elemento y confirma que desaparezca instantáneamente de la UI.

> **Nota para Antigravity/VS Code:** Asegúrate de tener instalada la extensión de Dart y Flutter. El agente `.agents` podrá sugerirte mejoras en el diseño de las tarjetas (Flashcards) una vez que el CRUD de mazos sea estable.
