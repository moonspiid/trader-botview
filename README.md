# Trader BotView

Page HTML unique qui analyse un marché Binance en direct et génère une configuration de bot **prête à copier** : grille, DCA ou suivi de tendance.

Aucune installation, aucun compte, aucune clé API. Tout tourne dans le navigateur.

**[Ouvrir l'outil](https://moonspiid.github.io/trader-botview/)** · données publiques Binance

---

## Ce que fait l'outil

1. **Analyse** une paire Spot Binance sur 15m, 1h, 4h ou 1d (500 bougies).
2. **Calcule** les indicateurs : EMA 20/50/200, RSI 14, MACD, ATR 14, Bollinger 20/2, ADX 14 avec +DI/-DI, ratio de volume, spread et déséquilibre du carnet.
3. **Détecte le régime** : range, tendance haussière, tendance baissière ou transition.
4. **Choisit une stratégie** et produit ses paramètres chiffrés :
   - **Grid neutre** : borne basse, borne haute, nombre de grilles, pas, profit par grille net de frais, quantité par grille, stop de sécurité.
   - **DCA adaptatif** : budget alloué, ordre de base, 6 renforts avec prix et montants, PRU, take-profit, invalidation.
   - **Suivi de tendance** : entrée, stop ATR, deux objectifs, taille de position, levier nécessaire, prix de liquidation estimé.
   - **Attendre** : conditions précises à surveiller quand le marché ne s'y prête pas.
5. **Vérifie la faisabilité** : pas de grille supérieur aux frais, taille minimale (`minNotional`), `tickSize` / `stepSize`, liquidité, spread, funding.
6. **Génère cinq blocs copier-coller** : rapport texte, JSON de configuration, paramètres d'ordre Binance, exemples cURL/Python signés, checklist de sécurisation.

## Ce que l'outil ne fait pas

- Il ne se connecte pas à votre compte et n'envoie aucun ordre.
- Il ne demande jamais de clé API.
- Il ne prédit pas les prix.

## Utilisation

### En ligne

Ouvrir la page GitHub Pages du dépôt.

### En local

```bash
git clone https://github.com/moonspiid/trader-botview.git
cd trader-botview
python3 -m http.server 8000
```

Puis ouvrir <http://localhost:8000>.

Un simple double-clic sur `index.html` fonctionne aussi dans la plupart des navigateurs, mais un serveur local évite les restrictions CORS.

## Sources de données

Données publiques Binance, sans authentification :

| Endpoint | Usage |
| --- | --- |
| `/api/v3/klines` | Bougies OHLCV |
| `/api/v3/ticker/24hr` | Prix, variation, volume, plus haut/bas |
| `/api/v3/depth` | Spread et déséquilibre du carnet |
| `/api/v3/exchangeInfo` | Filtres de la paire (tick, step, minimum) |
| `fapi/v1/premiumIndex` | Funding (optionnel, si disponible) |

La page essaie plusieurs hôtes dans l'ordre (`api.binance.com`, `data-api.binance.vision`, `api-gcp.binance.com`, `api4.binance.com`) et conserve celui qui répond. `data-api.binance.vision` sert de secours quand l'accès direct est bloqué.

## Paramètres ajustables

| Champ | Effet |
| --- | --- |
| Capital (USDT) | Base de tous les calculs de taille |
| Risque / trade % | Risque par position pour la stratégie de tendance |
| Frais / ordre % | 0,1 % spot par défaut (0,075 % avec BNB) ; sert au calcul du profit net par grille |
| Levier max | Plafonne la taille en futures ; 1 = spot uniquement |

Les paramètres sont mémorisés dans le navigateur (`localStorage`), ainsi que la dernière paire et l'unité de temps.

## Structure

```
index.html   page unique : styles, logique d'analyse, graphique canvas, générateurs de config
README.md
LICENSE
```

Aucune dépendance, aucun build. Le graphique est dessiné en canvas, les indicateurs sont recalculés côté client.

## Avertissement

Outil éducatif. Ce n'est pas un conseil financier. Les performances passées ne préjugent pas des performances futures. Un bot mal configuré peut perdre tout le capital engagé. Testez toujours sur [testnet.binance.vision](https://testnet.binance.vision) avant d'engager de l'argent réel.

## Licence

MIT
