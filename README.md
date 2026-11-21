# Awan Academy — Web Portal

This is a browser-based conversion of the uploaded Awan Academy Android app. It retains the original academy logo and fonts and reimplements the student portal and admin workflows as a responsive React/Vite website. The website is independent of the original Android ZIP.


## Converted features

### Student portal
- Google sign-in via Firebase Authentication.
- Responsive registration form for student/father name, class, section, academy roll number, student number, registration number, examination session, subject selection, and terms acceptance.
- Subject catalog filtered by class and search, using the original subject catalog.
- Registration token and paper-code allocation with Firestore counters/transaction.
- Registration history for the signed-in student.
- QR generation and registration-token/verification-ID lookup.
- Receipt PDF and 14-page examination package PDF (hall ticket, answer-sheet pages, MCQ/subjective sheet, blank result card).
- Result card PDF download after results are posted.

### Admin workspace
- Admin overview dashboard and class registration totals.
- Registration search/filter, CSV export, edit and delete.
- QR camera scanner (browser camera permission required) and token lookup for attendance; mark Present/Late/Absent, with an update confirmation if an attendance record already exists.
- Results entry, calculated totals/percentage, pass/fail status, remarks, result-card PDF and result export/summary.
- Subjects add/edit/delete, class assignment, active/inactive and ordering.
- Examination timing management (up to two slots).
- Academy name/heading/instructions and logo/stamp/signature image uploads.
- Admin password update using browser Web Crypto PBKDF2-SHA256.


## Limitations and external services

- **Firebase is required** for Google authentication, cloud database persistence, and admin/student data. No local mock database is used as a substitute.
- Firebase client configuration is provided via `.env.local`, not committed. Client Firebase config is not a private credential, but the project must have Firebase Authentication and restrictive Firestore rules enabled.
- Google sign-in requires the web app's authorized domain to be added in Firebase Authentication.
- Admin camera scanning requires HTTPS or localhost, browser camera access, and a supported camera device. Manual token entry remains available.
- PDF export is browser-side. The 14-page package layout is an approximation of the Android document design and is not pixel-identical to the original Android PDFs.
- Branding image assets are compressed before upload; Firestore rules limit each image field to 400,000 characters.
- Android-only Google Credential Manager, native sharing/printing, and public-download-folder behavior are replaced by browser sign-in, direct PDF downloads, and camera scanning.
- The Android project contained a bootstrap admin email. It has been replaced in this website project with `bootstrap-admin@example.com` to avoid shipping the account address. Replace this placeholder with the intended verified owner email in **both** `.env.local` and `firestore.rules` before deploying rules. First-admin setup is restricted to that verified Google account.
- The Android Firebase configuration file and its project-specific identifiers are intentionally not copied. Use the Firebase project you own/control and configure the website's web app settings.


## Windows local setup

### 1. Install software
1. Install **Node.js LTS (20 or newer)** from <https://nodejs.org/>. This includes npm.
2. Install **Visual Studio Code** from <https://code.visualstudio.com/>.
3. Install Git only if you plan to deploy through GitHub/Vercel/Netlify.

### 2. Extract and open
1. Download and extract the `awan-academy-web.zip` file.
2. Open VS Code.
3. Choose **File → Open Folder…** and select the extracted `awan-academy-web` folder.
4. In VS Code, choose **Terminal → New Terminal**. Make sure the terminal is in the project folder (the folder containing `package.json`).

### 3. Install dependencies
Run this command in the VS Code terminal:

```bash
npm install
```

### 4. Configure Firebase
1. Open <https://console.firebase.google.com/> and select/create a Firebase project.
2. In **Project settings → General**, create a Web App if one isn't already registered. Copy the web app config values.
3. Enable **Authentication → Sign-in method → Google**.
4. Add `localhost` under Authentication → Settings → Authorized domains.
5. Create/enable **Firestore Database**.
6. Copy `.env.example` to `.env.local` in the project root.
7. Fill the Firebase web configuration values in `.env.local`:

```env
VITE_FIREBASE_API_KEY=your-web-api-key
VITE_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your-project-id
VITE_FIREBASE_STORAGE_BUCKET=your-project.appspot.com
VITE_FIREBASE_MESSAGING_SENDER_ID=your-sender-id
VITE_FIREBASE_APP_ID=your-web-app-id
VITE_BOOTSTRAP_ADMIN_EMAIL=your-verified-owner-email@example.com
```

The values can also be found in the Android `google-services.json` supplied in the original app (under `project_info` and `client`), if you are using that Firebase project. The Android config file itself is not included in this web ZIP.

### 5. Set up Firestore security rules
1. Open `firestore.rules` in VS Code.
2. Replace the placeholder `bootstrap-admin@example.com` in `isBootstrappedAdmin()` with the exact verified owner email you placed in `.env.local`.
3. Install Firebase CLI (once):

```bash
npm install -g firebase-tools
```

4. Sign in to Firebase CLI:

```bash
firebase login
```

5. Link the local project to your Firebase project:

```bash
firebase use --add
```

Choose your Firebase project when prompted. If needed, check available projects with `firebase projects:list`.
6. Deploy Firestore rules:

```bash
firebase deploy --only firestore:rules
```

**Do not deploy permissive rules.** The included rules restrict student access to their own records and require admin access for administrative collections.

### 6. Run the site locally

From the website project directory:

```bash
npm run dev
```

Vite will display a local URL, normally:


<http://localhost:5173>

Open that URL in Chrome, Edge, or Firefox. Use Google sign-in to access the registration form and your own records.

### 7. First administrator setup
1. Use the verified bootstrap-owner Google account you configured in `.env.local` and `firestore.rules` to sign in.
2. Open **Admin Login**.
3. Enter the owner email and choose an administrator password (the first login creates the administrator password record).
4. The initial admin setup seeds the original default subjects and examination timings if those Firestore collections are empty.
5. Additional Google accounts must be explicitly granted admin access in Firestore by the bootstrap administrator. The web interface intentionally does not let an ungranted account self-assign administrator access.

### Stop/restart
- Stop the development server by pressing **Ctrl + C** in the terminal.
- Restart with `npm run dev`.

### Production build

```bash
npm run build
```

The optimized website is written to the `dist` folder. To preview the production build locally:


```bash
npm run preview
```

The preview command will print a local URL, normally `http://localhost:4173`.


## Publish and share online

### Option A — Vercel (recommended)
1. Push the project to a GitHub repository. Do not commit `.env.local` (it is ignored).
2. Visit <https://vercel.com/> and choose **Add New → Project**.
3. Import the GitHub repository.
4. Framework preset: **Vite**. Build command: `npm run build`. Output directory: `dist`.
5. In the Vercel project **Settings → Environment Variables**, add all `VITE_FIREBASE_*` values and `VITE_BOOTSTRAP_ADMIN_EMAIL` from your `.env.local`.
6. Deploy. Vercel will provide a public URL such as `https://your-project.vercel.app`.
7. Add the new Vercel domain to Firebase Authentication → Authorized domains. Confirm your Firestore rules still have the correct bootstrap-owner email.
8. Open the public link to verify Google sign-in and registration. Share the link with students.

### Option B — Netlify
1. Push the project to GitHub and import it in <https://app.netlify.com/>.
2. Set build command to `npm run build` and publish directory to `dist`.
3. Add the Firebase environment variables in **Site configuration → Environment variables**.
4. Deploy, then add the Netlify domain to Firebase Authentication's Authorized domains.
5. Share the generated `https://...netlify.app` link.

### Option C — Firebase Hosting
1. Make sure Firebase CLI is installed and the project is linked as above.
2. Initialize Hosting:

```bash
firebase init hosting
```

3. Select your Firebase project, set the public directory to `dist`, configure as a single-page app (rewrite all URLs to `/index.html`), and do **not** overwrite the project `index.html`.
4. Build:

```bash
npm run build
```

5. Deploy Hosting:

```bash
firebase deploy --only hosting
```

6. Firebase Hosting provides a public `https://...web.app` or `https://...firebaseapp.com` URL. Add the domain to Firebase Authentication's authorized domains, test sign-in, then share the URL.


## Commands quick reference (PowerShell or CMD)

```bash
cd path\to\awan-academy-web
npm install
npm run dev
# Ctrl+C to stop
npm run build
npm run preview
```

## Project structure

```text
awan-academy-web/
├─ public/                 Original logo/icon/fonts
├─ src/
│  ├─ firebase.js          Firebase config and Google sign-in
│  ├─ main.jsx            Student portal + Admin UI and Firestore workflows
│  └─ style.css           Responsive UI styles
├─ firestore.rules         Firestore access rules (bootstrap email placeholder)
├─ firebase.json           Firebase CLI rules config
├─ .env.example            Environment variables template
├─ index.html              Vite entry HTML
├─ package.json            Dependencies and scripts
└─ README.md               This guide
```
