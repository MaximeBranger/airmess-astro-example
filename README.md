# Formulaire de contact Astro sans API route ni serveur

Créez un composant Astro de formulaire de contact qui envoie les messages par email, en gardant votre site 100 % statique.

Exemple complet et clonable, à utiliser avec [AirMess](https://airmess.fr/?utm_source=github&utm_medium=readme&utm_campaign=airmess-astro-example&utm_content=intro).

Autres exemples : [HTML](https://github.com/MaximeBranger/airmess-html-example) · [Hugo](https://github.com/MaximeBranger/airmess-hugo-example)

📖 Tutoriel complet : [https://airmess.fr/tutoriels/formulaire-contact-astro](https://airmess.fr/tutoriels/formulaire-contact-astro?utm_source=github&utm_medium=readme&utm_campaign=airmess-astro-example&utm_content=tutoriel)

## Démarrage rapide

```bash
git clone https://github.com/MaximeBranger/airmess-astro-example.git
cd airmess-astro-example
```

```bash
cp .env.example .env   # puis remplacez VOTRE_TOKEN par le jeton de votre formulaire AirMess
npm install
npm run dev
```

Ajoutez l'origine locale (ex. `http://localhost:4321`) à la liste des origines autorisées du formulaire.

## Garder Astro statique

Avec Astro, on peut traiter un formulaire dans une API route, mais cela impose un adaptateur et un mode serveur. Si votre site est en `output: 'static'`, AirMess évite ce changement.

## Étape 1 : le composant

Créez `src/components/ContactForm.astro` :

```astro
---
interface Props {
  formUrl?: string;
}
const { formUrl = import.meta.env.PUBLIC_AIRMESS_URL } = Astro.props;
---

<form id="contact-form" action={formUrl} method="POST">
  <label for="name">Nom</label>
  <input id="name" name="name" type="text" required />
  <label for="email">Email</label>
  <input id="email" name="email" type="email" required />
  <label for="message">Message</label>
  <textarea id="message" name="message" rows="5" required></textarea>
  <button type="submit">Envoyer</button>
  <p id="contact-status" role="status"></p>
</form>

<script>
  const form = document.querySelector<HTMLFormElement>('#contact-form')!;
  const status = document.querySelector('#contact-status')!;

  form.addEventListener('submit', async (e) => {
    e.preventDefault();
    const button = form.querySelector('button')!;
    button.disabled = true;
    status.textContent = 'Envoi en cours…';

    try {
      const res = await fetch(form.action, { method: 'POST', body: new FormData(form) });
      if (!res.ok) throw new Error(String(res.status));
      form.reset();
      status.textContent = 'Merci, votre message a bien été envoyé.';
    } catch {
      status.textContent = "L'envoi a échoué. Réessayez dans un instant.";
    } finally {
      button.disabled = false;
    }
  });
</script>
```

## Étape 2 : la variable d'environnement

Dans `.env` :

```bash
PUBLIC_AIRMESS_URL=https://airmess.fr/api/submit/VOTRE_TOKEN
```

Le préfixe `PUBLIC_` est nécessaire pour qu'Astro l'expose au build. L'URL n'est pas un secret, puisqu'elle apparaît dans le HTML, mais la variable permet d'utiliser une URL différente en préproduction.

## Étape 3 : l'utiliser

```astro
---
import ContactForm from '../components/ContactForm.astro';
---
<h1>Contact</h1>
<ContactForm />
```

Astro traite et regroupe automatiquement le `<script>` du composant : pas de configuration supplémentaire.

## Licence

[MIT](LICENSE)
