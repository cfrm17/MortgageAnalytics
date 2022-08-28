# Mortgage Analytics

Mortgage model is used to calculate the risk profile of various interest rate risk sensitive instruments, such as fixed rate mortgage and adjustable risk mortgage.

The interest rate risk measures depend on the specific characteristics of the instrument, and these are calculated different for the various instruments assuming an underlying interest rate process together with various valuation models for the instruments. We will begin by outlining the interest rate process, then proceed to discuss the various instruments separately.

The various interest rates are modelled using a choice of arbitrage free term structure models. In the current implementation, a Black-Karasinski (BK) short rate model is used. 

There are several alternatives in how one chooses to implement the BK model. Here  a(t) is chosen to be a constant (a(t) = 0.03 for all t). The other parameter (σ(t)) is chosen to be a constant that best matches a vector of yield volatilities. The yield can be computed as https://finpricing.com/lib/FiZeroBond.html.

In order to calibrate σ(t), one need to first derive the volatilities of the yield curve points. We use treasury futures options to derive implied volatilities of futures prices, and then uses these future price volatilities to derive yield volatilities.

For fixed rate mortgages, the mortgage coupon is predetermined and constant over the life of the mortgage. The note rate (or coupon) is further split as:

• Note Rate (or Gross WAC). This is what the mortgagee actually pays, and this is used to determine the prepayment speeds of the mortgage (see below).

• Coupon (or Net WAC). This is what the owner of the mortgage receives, and is used to value the mortgage. For the formulas above, rn is the net WAC divided by 12.

In all cases we examined, the difference between these two is a constant spread (25 bps). This depends on the mortgage driver type.

For adjustable rate mortgages, the coupon of the mortgage is usually fixed for some initial period, then reset at various other times. An example of a product specification is as follows:

The customer coupon (or note rate) is 5% for the first five years and then resets every year thereafter based on the 12 month LIBOR rate. The compounding frequency is this rate is money market (or simple), and a spread of 2.25% is added to determine the coupon. Furthermore, there are caps on the reset rates as specified above.

Thus, in order to determine the coupon, one needs the future interest rate scenarios from the short rate model to determine possible future 12 month LIBOR rates. The short rates are generated using Monte Carlo (with a BK process), and a “sub” lattice is generated at the reset period to determine the 12 month LIBOR rates and therefore the coupons.

The specification of a prepayment model is very important for the correct evaluation of these mortgages. The four factor model is comprised of the following observed characteristics of prepayment behaviour:

The base function accounts for prepayments in terms of the dependence on the available refinancing rate. The base function describes SMM as a function of refinancing incentive, which is the difference between the mortgage note rate and the current market rate. 

Generally speaking, the bigger this difference, the larger the prepayment. We have already discussed how the mortgage coupon is specified (for a given product), but we need to specify what the “current market rate” is.

The burnout accounts for the observed decrease in sensitivity of a pool to refinancing after a pool has experienced such opportunities in the past This factor corresponds to the observed decrease in prepayments as the mortgage (or pool of mortgages) ages. this is due to the fact that when a pool experiences a drop in market rats for the first time, those borrowers who are willing to take advantage and refinance do so, and the borrowers who are left in the pool are less responsive to changes in the market.

The seasoning refers to the fact that new pools take a certain amount of time before they assume their long-term prepayment behaviour. The seasonality captures the dependence of annual cyclical patterns. This factor has not been implemented.



