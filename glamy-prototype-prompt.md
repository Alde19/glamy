# Prompt — Prototype Glamy

Crée un prototype web statique d'une interface de réservation conversationnelle pour une nail artist appelée **Nadia**. Pas de base de données.

**Stack** : une seule page HTML avec CSS et JavaScript vanilla intégré. Zéro dépendance externe sauf une police Google Fonts.

## Mise en page générale

La page affiche un **smartphone en trompe-l'œil** centré sur un fond sombre légèrement texturé. Le smartphone a des bords arrondis, une encoche en haut, des boutons de volume sur le côté — comme un vrai iPhone. Toute la conversation se passe **à l'intérieur de l'écran du smartphone**.

En haut de l'écran du smartphone afficher :
- L'heure fictive **9:41** et les icônes signal/wifi/batterie
- En dessous, le nom **"Nadia 💅"** comme titre de la conversation

## Intelligence artificielle

La conversation est gérée par une vraie IA via l'API **OpenRouter** avec le modèle `deepseek/deepseek-v3-2`.

L'appel API se fait comme suit :

```javascript
fetch("https://openrouter.ai/api/v1/chat/completions", {
  method: "POST",
  headers: {
    "Authorization": "Bearer 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",
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    model: "deepseek/deepseek-v3-2",
    messages: conversationHistory
  })
})
```

La clé API est hardcodée dans le code à la place de `OPENROUTER_API_KEY_ICI`.

## System prompt à injecter au démarrage

```
Tu es Nadia, une nail artist professionnelle basée en France.
Tu parles au nom de Nadia et la cliente ne sait pas qu'elle parle à une IA.
Tu es chaleureuse, professionnelle et tu utilises des emojis avec parcimonie 💅.
Ton rôle est de guider la cliente pour réserver un rendez-vous en suivant ces étapes dans l'ordre :
1. Accueillir la cliente et lui proposer 3 prestations : Pose gel (1h), Remplissage (45min), Nail art (1h30)
2. Quand elle choisit une prestation, lui proposer des créneaux disponibles (invente 6 créneaux fictifs sur 3 jours cette semaine) elle doit cliquer sur les créneaux pour choisir.
3. Quand elle choisit un créneau, lui demander son prénom
4. puis demander son numéro de téléphone
5. puis demander une avance avant de confirmer la reservation. elle choisit entre paiement par carte bancaire ou paypal. la cliente choisit. 
elle met son numéro de carte (fake)
4. Confirmer le RDV avec un récapitulatif chaleureux

Pour les prestations et les créneaux, génère toujours ta réponse en JSON structuré comme ceci :
- Pour proposer des prestations : {"type": "prestations", "message": "...", "options": ["Pose gel (1h)", "Remplissage (45min)", "Nail art (1h30)"]}
- Pour proposer des créneaux : {"type": "creneaux", "message": "...", "options": ["Lundi 14h00", "Lundi 16h30", ...]}
- Pour une réponse texte simple : {"type": "texte", "message": "..."}

Réponds TOUJOURS en JSON valide, jamais en texte brut.
Tu parles uniquement en français.
```

## Rendu des réponses IA

- Si `type === "prestations"` ou `type === "creneaux"` → afficher le message + des boutons cliquables arrondis pour chaque option
- Si `type === "texte"` → afficher simplement le message
- Quand la cliente clique sur un bouton, envoyer ce choix comme message utilisateur à l'IA et l'ajouter dans l'historique de conversation

## Design intérieur du chat

- Couleurs douces, univers beauté/nail art — rose poudré, beige, touches dorées
- Les messages de Nadia ont un avatar rond avec l'initiale **N**
- Les messages de la cliente sont alignés à droite
- Les boutons de choix sont arrondis et élégants
- Une zone de saisie texte en bas du smartphone, toujours visible
- Animations douces sur l'apparition des messages (fade + slide up)
- Indicateur **"Nadia est en train d'écrire..."** avec 3 points animés pendant l'appel API

## Comportement au démarrage

L'IA envoie automatiquement le premier message d'accueil dès le chargement de la page.

