Te dejo un ejemplo claro y práctico usando **MongoDB** (base de datos no relacional) para una app de **flashcards**. Aquí verás:

* Base de datos
* Colecciones
* Documentos
* Atributos con tipo de dato

---

## 🧠 1. Crear la base de datos

```js
use flashcardsDB
```

---

## 📂 2. Colección: usuarios

Guarda la información de quienes usan la app.

```js
db.usuarios.insertMany([
{
  _id: ObjectId(),
  nombre: "Yared",
  email: "yared@email.com",
  contraseña: "123456",
  fecha_registro: new Date(),
  activo: true
}
])
```

### 📌 Atributos y tipos:

* `_id`: ObjectId
* `nombre`: String
* `email`: String
* `contraseña`: String
* `fecha_registro`: Date
* `activo`: Boolean

---

## 📚 3. Colección: mazos (deck de flashcards)

```js
db.mazos.insertMany([
{
  _id: ObjectId(),
  titulo: "Biología básica",
  descripcion: "Conceptos fundamentales",
  id_usuario: ObjectId("ID_DEL_USUARIO"),
  fecha_creacion: new Date(),
  publico: true
}
])
```

### 📌 Atributos:

* `_id`: ObjectId
* `titulo`: String
* `descripcion`: String
* `id_usuario`: ObjectId (relación)
* `fecha_creacion`: Date
* `publico`: Boolean

---

## 🃏 4. Colección: flashcards

```js
db.flashcards.insertMany([
{
  _id: ObjectId(),
  pregunta: "¿Qué es la célula?",
  respuesta: "Unidad básica de la vida",
  id_mazo: ObjectId("ID_DEL_MAZO"),
  dificultad: "media",
  fecha_creacion: new Date(),
  ultima_revision: null,
  repeticiones: 0
}
])
```

### 📌 Atributos:

* `_id`: ObjectId
* `pregunta`: String
* `respuesta`: String
* `id_mazo`: ObjectId
* `dificultad`: String (baja, media, alta)
* `fecha_creacion`: Date
* `ultima_revision`: Date o null
* `repeticiones`: Number

---

## 📊 5. Colección: progreso (seguimiento del usuario)

```js
db.progreso.insertMany([
{
  _id: ObjectId(),
  id_usuario: ObjectId("ID_USUARIO"),
  id_flashcard: ObjectId("ID_FLASHCARD"),
  nivel: 3,
  correcta: true,
  fecha: new Date()
}
])
```

### 📌 Atributos:

* `_id`: ObjectId
* `id_usuario`: ObjectId
* `id_flashcard`: ObjectId
* `nivel`: Number (nivel de aprendizaje)
* `correcta`: Boolean
* `fecha`: Date

---

## 🔗 Relación entre colecciones

* Un **usuario** tiene muchos **mazos**
* Un **mazo** tiene muchas **flashcards**
* Un **usuario** tiene progreso en muchas **flashcards**

---

## 💡 Ejemplo visual (documento completo)

```json
{
  "usuario": "Yared",
  "mazo": "Matemáticas",
  "flashcard": {
    "pregunta": "¿Cuánto es 2+2?",
    "respuesta": "4",
    "dificultad": "baja"
  }
}
```

---

Si quieres, puedo ayudarte a:

* Generar **40 flashcards listas para insertar** (como tu tarea anterior)
* Hacer el **modelo en JSON completo**
* O adaptarlo a tu app en **Flutter**

Solo dime 👍
