# Mis gastos

App web para llevar ingresos, gastos, transferencias y saldos por cuenta, mes a mes.
Funciona en PC y celular, se instala como app y los datos se comparten online entre quienes tengan acceso.

Archivos: `index.html` (la app), `firebase-config.js` (**el único que tenés que editar**), `sw.js`, `manifest.webmanifest` y los dos íconos.

---

## 1. Datos online (Firebase) — se hace una sola vez, ~10 minutos

Firebase es el servicio gratuito de Google que guarda los datos y los sincroniza entre dispositivos.
El plan gratuito sobra para uso familiar.

1. Entrá a https://console.firebase.google.com con tu cuenta de Google → **Agregar proyecto** (nombre libre, ej. `mis-gastos`). Podés desactivar Google Analytics.
2. **Authentication** → *Comenzar* → pestaña *Sign-in method* → habilitá **Correo electrónico/contraseña** (solo la primera opción).
3. **Firestore Database** → *Crear base de datos* → ubicación `southamerica-east1` (São Paulo) → modo **producción**.
4. En Firestore, pestaña **Reglas**: borrá todo, pegá esto y reemplazá los dos emails por los de ustedes (en minúsculas). Después *Publicar*:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read, write: if request.auth != null
           && request.auth.token.email_verified == true
           && request.auth.token.email in ['tu-email@gmail.com', 'email-de-tu-pareja@gmail.com'];
       }
     }
   }
   ```
   Con esto solo esos dos emails (ya confirmados) pueden leer o escribir. Cualquier otra persona es rechazada.
5. **Configuración del proyecto** (engranaje arriba a la izquierda) → *Tus apps* → ícono `</>` (Web) → registrá la app (nombre libre, sin Hosting) → copiá los valores de `firebaseConfig`.
6. Pegalos en `firebase-config.js` (apiKey, authDomain, projectId, etc.). Guardá.
7. Una vez publicada la web (paso 2 de abajo): en Firebase → **Authentication → Configuración → Dominios autorizados** → agregá `TU-USUARIO.github.io`.

Primer uso: abrí la web → **Crear cuenta** con tu email → confirmá el mail que te llega (mirá en spam) → tocá "Ya lo confirmé".
Después ⚙ → **Importar desde Excel** para traer tu `Control_de_Saldos.xlsx`.
Tu pareja hace lo mismo: crea su cuenta con su email y ya ve todo en tiempo real.

> Los valores de `firebase-config.js` no son secretos; pueden estar en un repositorio público. La seguridad la dan las reglas del paso 4.

---

## 2. Publicar en GitHub Pages (gratis)
1. En github.com creá un repositorio nuevo (ej. `mis-gastos`).
2. Subí todos los archivos de esta carpeta.
   (Desde VS Code: `git init`, `git add .`, `git commit -m "primera versión"` y seguí los pasos que te da GitHub para `git push`.)
3. En el repositorio: **Settings → Pages → Deploy from a branch → main / (root) → Save**.
4. En uno o dos minutos queda en `https://TU-USUARIO.github.io/mis-gastos/`.

## 3. Instalarla en el celular
- **Android (Chrome):** menú ⋮ → *Instalar app*.
- **iPhone (Safari):** Compartir → *Agregar a pantalla de inicio*.

## Probarla en tu PC
Con la extensión **Live Server** de VS Code: clic derecho en `index.html` → *Open with Live Server*.
Si `firebase-config.js` está vacío, corre en *modo local de prueba* (datos solo en ese navegador).

---

## Cosas útiles
- **Sin conexión:** podés cargar gastos igual; se suben solos cuando vuelve internet.
- **Quién cargó qué:** cada gasto e ingreso muestra el nombre (sale de la primera parte del email).
- **Copias:** ⚙ → *Guardar copia de seguridad* o *Exportar a Excel* de vez en cuando.
- **"Borrar todo"** borra para todos los que comparten la app.
- **Si cambiás el código y no ves cambios en el celular:** en `sw.js` subí el número de `CACHE` (`mis-gastos-v3`…).
- **Costos:** el plan gratuito de Firebase (Spark) permite miles de lecturas y escrituras por día; un uso familiar no se acerca.
