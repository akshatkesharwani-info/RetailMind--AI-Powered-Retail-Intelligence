
# RetailMind: AI-Powered Retail Intelligence

Customer segmentation, product bundles, a tested product recommender, and an "ask your data" assistant for a retail store. Groq writes the marketing drafts and answers questions, but only from numbers calculated in code, and it is not allowed to invent offers.

Built in Google Colab with Groq (`openai/gpt-oss-120b`).

> **The 9,000 orders are made up.** Product bundles were planted in the generator on purpose, so the recommender and basket results show the method, not a real store. The notebook also accepts your own `transactions.csv` with columns `invoice, customer_id, date, product, category, quantity, price`.

## What it does

1. **KPIs and revenue trend**, with months compared as **revenue per day** (February has fewer days than other months).
2. **Is a "weak month" real?** The order dates are shuffled 500 times to see how big a best-to-weakest gap random timing alone produces.
3. **RFM segmentation** (recency, frequency, monetary value) with KMeans into 4 segments. The group with the longest time since last purchase is named "At Risk"; the others are named by spend.
4. **Market basket analysis:** support, confidence and lift for product pairs.
5. **Product recommender, tested honestly.** Pair scores are built from the first 80% of days and tested on the last 20%, against a "most popular products" baseline.
6. **Marketing drafts per segment (Groq).** Each message mentions the product that segment spends the most on. The AI is forbidden to invent discounts, prices, promo codes, deadlines or customer statistics, and writes `[OFFER]` where the real offer goes.
7. **Ask your data (Groq).** The AI reads a small calculated summary, not the raw table, and answers questions from it.

## Results from the run

| Measure | Result |
|---|---|
| Revenue / orders / customers | 8,253,945 / 9,000 / 679 |
| Average order value | 917.1 |
| Top category | Grocery, 58.8% of revenue |

**Segments:**

| Segment | Customers | Days since last purchase | Orders | Share of revenue |
|---|---|---|---|---|
| Champions | 129 | 7.6 | 37.4 | 53.9% |
| Loyal | 195 | 14.9 | 13.5 | 29.7% |
| Potential | 189 | 52.6 | 6.5 | 13.4% |
| At Risk | 166 | 160.0 | 1.9 | 3.0% |

- **The top 19% of customers (Champions) bring in 53.9% of revenue.**
- **Segments differ in how much and how often they buy, not in what they buy.** Each segment's product mix is within about 2% to 9% of everyone else's (index 0.98 to 1.09).
- **Best bundle:** Soap + Toothpaste (lift 2.96). Bundles were planted in this made-up data.
- **Recommender:** hit rate **50.4%** against **38.0%** for "most popular", on 1,475 later baskets with 2+ items. A hit means at least one other item in the basket was among the 3 recommendations.
- **No weak month.** January was the weakest and December the best on revenue per day (gap 12.3%), but a gap that large or larger appeared in **74% of random shuffles of the dates**. The notebook says there is no clear weak month, and the AI follows that when asked.

## What the evaluation showed

- **Always test a recommender against "most popular".** Without that baseline, a hit rate of 50% means nothing.
- **A "weak month" needs a chance check.** Comparing months by total revenue would have blamed February's 28 days; comparing per day still left a gap that random timing explains.
- **Draft messages must not borrow internal statistics.** An earlier version wrote things like "It's been 12 days since your last coffee treat" using a segment average as if it were a fact about one customer, and invented discounts and promo codes. The prompt now only allows `[Name]`, the product and `[OFFER]`.

## Limitations

- Made-up data with planted bundles, so the recommender gap is flattering. On real data it is usually smaller.
- The segment names are rule-based labels on KMeans clusters, not validated customer types.
- The AI-written messages are drafts for a human to edit and a real offer must replace `[OFFER]`.
- Basket analysis ignores time and quantity, and the recommender ignores customer history.

## Tech stack

pandas, scikit-learn, matplotlib, seaborn, Groq API.

## How to run

1. Open the notebook in Google Colab.
2. Run the cells from top to bottom.
3. When asked, paste your own Groq API key (free at console.groq.com). It is hidden while typing and is never saved in the notebook.

## Files the notebook creates

- `retailmind_rfm_segments.csv`: customers with recency, frequency, monetary value and segment
- `retailmind_basket_rules.csv`: product pairs with support, confidence and lift
- `retailmind_transactions.csv`: the transactions with segment added

---

Built by **Akshat Kesharwani** | [GitHub](https://github.com/akshatkesharwani-info) | [Portfolio](https://akshatkesharwani-info.github.io/)
