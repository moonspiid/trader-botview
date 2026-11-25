# Trader BotView

<p>
  <img alt="Binance live data" src="https://img.shields.io/badge/data-Binance%20live-f0b90b?labelColor=30363d">
  <img alt="no API key" src="https://img.shields.io/badge/API%20key-not%20required-2ea043?labelColor=30363d">
  <img alt="single HTML file" src="https://img.shields.io/badge/single-HTML%20file-1f6feb?labelColor=30363d">
  <img alt="no build" src="https://img.shields.io/badge/build-none-8957e5?labelColor=30363d">
  <br>
  <img alt="grid, DCA, trend" src="https://img.shields.io/badge/bots-grid%20%C2%B7%20DCA%20%C2%B7%20trend-ea7a15?labelColor=30363d">
  <img alt="copy paste configs" src="https://img.shields.io/badge/configs-copy%20%26%20paste-3fb950?labelColor=30363d">
  <img alt="MIT license" src="https://img.shields.io/badge/license-MIT-2ea043?labelColor=30363d">
  <img alt="price, free" src="https://img.shields.io/badge/price-free-2ea043?labelColor=30363d">
</p>

**Le marché sous les yeux, la config dans le presse-papiers.** Un tableau de bord plein écran, sans défilement : il lit Binance en direct, choisit la stratégie de bot la plus adaptée, et sort les paramètres exacts — grille, DCA ou suivi de tendance — avec les niveaux de stop et de take-profit prêts à copier.

<h3 align="center">
  <a href="https://moonspiid.github.io/trader-botview/" target="_blank" rel="noopener noreferrer">↖ Ouvrir l'outil</a>
</h3>

<p align="center">
  Aucun compte, aucun serveur, aucune installation — ouvrez la page et lisez le marché.
  <br>
  Mode manuel pour choisir votre paire, mode auto pour laisser le scanner choisir. Aucune clé API demandée, aucun ordre envoyé.
</p>

## Sommaire

- [Pourquoi](#pourquoi)
- [Fonctionnalités](#fonctionnalités)
- [Démarrage rapide](#démarrage-rapide)
- [Comment ça marche](#comment-ça-marche)
- [Bon à savoir](#bon-à-savoir)
- [Soutenir le projet](#soutenir-le-projet)
- [Licence](#licence)

## Pourquoi

Configurer un bot Binance demande de choisir une foule de nombres : bornes d'une grille, nombre de niveaux, pas, montant par ordre, stop, take-profit. La plupart des gens les devinent, ou copient ceux d'un inconnu sur un forum.

Trader BotView fait le calcul à partir du marché réel : volatilité, tendance, liquidité, spread et frais. Il propose une stratégie, chiffre chaque paramètre, vérifie qu'il respecte les filtres Binance de la paire, et donne le tout en blocs copiables.

## Fonctionnalités

- **Tableau de bord plein écran** — trois volets sur une seule page, sans défilement : commandes et marché à gauche, graphique et plan au centre, configurations copiables à droite. Seuls les blocs de texte défilent, à l'intérieur de leur panneau.
- **Mode manuel** — vous choisissez la paire, l'unité de temps et le capital, puis **Analyser**.
- **Mode auto** — vous saisissez votre capital et le scanner compare **8 paires × 1h et 4h**, classe les 16 combinaisons sur 100 selon la confiance, le spread, la liquidité et le rendement attendu, puis charge la meilleure. Changer le capital relance le scan.
- **Analyse en direct** — 500 bougies Binance en 15m, 1h, 4h ou 1d, plus le ticker 24 h et le carnet d'ordres.
- **Indicateurs calculés dans le navigateur** — EMA 20/50/200, RSI 14, MACD 12/26/9, ATR 14, Bollinger 20/2, ADX 14 avec +DI/−DI, ratio de volume, spread et déséquilibre du carnet.
- **Détection de régime** — range, tendance haussière, tendance baissière ou transition. Le régime décide de la stratégie, pas l'inverse.
- **Trois stratégies chiffrées** :
  - **Grid neutre** : bornes centrées sur le prix, nombre de grilles, pas, profit net par grille après frais, quantité par grille, stop de sécurité.
  - **DCA adaptatif** : budget plafonné, ordre de base, six renforts avec prix et montants, prix de revient, take-profit, invalidation.
  - **Suivi de tendance** : entrée, stop à 2 ATR, deux objectifs, taille de position, levier nécessaire, prix de liquidation estimé.
- **Quatrième réponse possible : attendre** — quand le spread est trop large, la liquidité insuffisante ou la tendance trop violente, l'outil donne les conditions précises à surveiller au lieu de forcer un bot.
- **Grille de paramètres copiables** — sous le graphique, chaque valeur du bot a sa case et son bouton **Copier** : borne basse, borne haute, nombre de grilles, pas de grille, montant par grille, montant total, quantité par grille, mode de grille (arithmétique/géométrique), **mode de marge** (Spot ou Isolated), TP et SL. Une valeur par appui, donc rien à sélectionner au doigt sur mobile.
- **Niveaux SL / TP copiables** — entrée, stop, TP1, TP2 et quantité, chacun avec son bouton de copie, plus un ordre de sortie prêt à coller (OCO en spot, `STOP_MARKET` + `TAKE_PROFIT_MARKET` en futures, `TAKE_PROFIT_LIMIT` pour le DCA).
- **Cinq blocs de sortie** : rapport texte, JSON de configuration complet (avec les niveaux de grille), paramètres d'ordre Binance, cURL et Python signés sur le testnet, checklist de sécurisation.
- **Vérifications de faisabilité** — `tickSize`, `stepSize`, `minQty`, `minNotional`, profit par grille supérieur aux frais, liquidité 24 h, funding.
- **Zéro dépendance** — pas de framework, pas de build, pas de requête vers un serveur tiers : le graphique est dessiné en canvas et tout le calcul se fait dans la page.

## Démarrage rapide

1. Ouvrir **[la version en ligne](https://moonspiid.github.io/trader-botview/)**.
2. Choisir un mode : **Manuel** (vous choisissez la paire) ou **Auto** (le scanner propose les meilleures options pour votre capital).
3. En manuel : paire, unité de temps, capital, puis **Analyser**. En auto : capital, puis **Scanner le marché** (le scan démarre tout seul au premier passage).
4. Lire le **Diagnostic** et le **Plan recommandé**, puis descendre sur **Configurations prêtes à copier**.
5. Copier les paramètres un par un dans la grille sous le graphique (bornes, grilles, montant, marge, TP/SL), ou l'onglet **SL / TP** pour les niveaux et l'ordre de sortie complet.
6. Tester sur [testnet.binance.vision](https://testnet.binance.vision) avant tout argent réel.

### En local

```bash
git clone https://github.com/moonspiid/trader-botview.git
cd trader-botview
python3 -m http.server 8000
```

Puis ouvrir <http://localhost:8000>. Un double-clic sur `index.html` fonctionne aussi dans la plupart des navigateurs, mais un serveur local évite les restrictions CORS.

## Comment ça marche

```
index.html        page unique : styles, indicateurs, graphique canvas, générateurs de config
assets/logo.svg   logo du projet
README.md
LICENSE
```

- **Données** — `/api/v3/klines`, `/api/v3/ticker/24hr`, `/api/v3/depth` et `/api/v3/exchangeInfo`. La page essaie plusieurs hôtes dans l'ordre (`api.binance.com`, `data-api.binance.vision`, `api-gcp.binance.com`, `api4.binance.com`) et garde celui qui répond.
- **Calculs** — EMA, RSI de Wilder, MACD, ATR de Wilder, Bollinger, ADX de Wilder, ratios de volume, spread et déséquilibre du carnet.
- **Décision** — le régime et la qualité d'exécution (spread, liquidité) déterminent la stratégie ; les paramètres sont ensuite dérivés de l'ATR et du capital.
- **Scan auto** — `scanMarket()` interroge 8 paires sur 2 unités de temps par lots de 4 requêtes, note chaque couple avec `scoreCandidate()`, puis applique le même moteur d'analyse à la meilleure option.
- **Contraintes Binance** — chaque prix et chaque quantité sont arrondis au `tickSize` et au `stepSize` de la paire, et comparés au `minNotional`.
- **Mémoire** — paire, unité de temps et paramètres sont conservés dans le navigateur (`localStorage`).
- Pousser sur `main` republie le site via GitHub Pages.

## Bon à savoir

- Un range de grille est **centré sur le prix courant** et sa largeur est plafonnée par la volatilité, pour éviter une plage de 40 % avec trois ordres utiles.
- Le budget d'un DCA est plafonné à **25 % du capital en tendance baissière**, 50 % sinon. Le reste est signalé comme réserve.
- Un DCA n'a pas de stop-loss : l'outil affiche une **invalidation**, le niveau sous lequel on arrête les renforts.
- L'outil ne se connecte pas à votre compte, ne demande aucune clé et n'envoie aucun ordre. Les performances passées ne préjugent pas des performances futures : un bot peut perdre tout le capital engagé.
- Les données publiques Binance peuvent être bloquées dans certains pays ; l'hôte `data-api.binance.vision` sert alors de secours.

## Soutenir le projet

L'outil est gratuit, sans compte, sans publicité et sans tracking, et il n'y a pas d'offre payante à la clé. S'il vous fait gagner du temps, un don paie les heures derrière et les prochaines fonctionnalités.

**Don en USDT →** une seule adresse crypto, pas de carte, pas de compte, pas de processeur de paiement entre les deux.

<p align="center">
  <img src="https://raw.githubusercontent.com/moonspiid/ai-agent-multi-model/master/donate-usdt-qr.png" alt="QR code de l'adresse de don EVM" width="200">
  <br>
  <sub>À scanner avec n'importe quel wallet</sub>
</p>

```
0xA2700896E16BE762467D458F2B45260f8FAC9F3e
```

C'est une adresse EVM classique : **la même adresse fonctionne sur tous les réseaux EVM**. Envoyez l'USDT sur celui qui vous arrange :

| Réseau | Token |
| --- | --- |
| BNB Smart Chain (BEP20) | USDT |
| Ethereum (ERC20) | USDT |
| Polygon | USDT |
| Arbitrum One | USDT |
| Base | USDT |
| Optimism | USDT |

Deux règles : envoyez **uniquement de l'USDT** (un autre token envoyé à cette adresse ne peut pas être récupéré), et gardez un peu de la monnaie du réseau (BNB, ETH, POL) dans votre wallet pour payer les frais. Un réseau non-EVM comme Tron (TRC20) utilise un autre format d'adresse : rien de ce qui y est envoyé n'arrive ici.

## Licence

[MIT](LICENSE) © 2026 moonspiid — utilisez-le, modifiez-le, publiez-le.
