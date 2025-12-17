| **title** | **summary** |
| --- | --- |
| Profitable multi-step liquidations | Carefully sequenced multi-step liquidations can yield extra profit beyond what the protocol intends, due to the math and rounding behavior. |
| User’s health factor can decrease after a borrow or withdraw operation and be immediately liquidatable | Borrow/withdraw flows can reduce a user’s health factor below the liquidation threshold within the same transaction after checks. |
| Deficit / bad-debt reporting can be blocked via micro-collateral or front-running | A user can keep a tiny second collateral so activeCollateralCount > 1, preventing deficit reporting and delaying bad-debt accounting. |
| Liquidation revert due to unrelated paused asset in Hub | Liquidations can revert when an unrelated asset is paused at the Hub level, blocking liquidations even on healthy reserves. |
| Liquidations can worsen a position’s health factor even when the liquidation bonus is zero | Liquidation ordering and rounding can reduce a borrower’s health factor and over-seize collateral even when liquidation bonus is zero. |
| Rounding down on collateralToLiquidate can prevent liquidation | Rounding collateralToLiquidate down to zero in edge cases can prevent liquidations for undercollateralized positions. |
| Array.sort() can revert due to EVM stack limit | Sorting large in-memory arrays with Array.sort() can hit the EVM stack limit and revert, breaking operations that rely on it. |
| Assets with assetId above 65536 cannot be added to a Spoke | Spokes reject assetId > 65535, so Hub assets with large IDs cannot be added as reserves despite being valid. |
| Fees can be minimized by accruing often | Calling interest accrual too frequently can reduce effective fees due to how fee growth is computed per accrual step. |
| Front running risks | Certain operations can be front-run to change system state (e.g. risk premium, positions) in ways that harm or grief other users. |
| Lowering riskPremiumThreshold can temporarily block premium refresh and user actions | Lowering riskPremiumThreshold below existing premium values makes premium refresh revert, temporarily blocking user actions that trigger it. |
| Missing events | Some important state-changing calls emit no events, making monitoring and off-chain accounting more difficult. |
| Multicall operations can be front-run and forced to revert | multicall sequences can be front-run so later steps revert, causing grief and wasting gas for callers. |
| Overflow in config key makes it possible to update collateral factor to 0 | Misconfigured or overflowing config keys can allow the effective collateral factor to be updated to zero unexpectedly. |
| Slippage in liquidations | Rounding and price conversion in liquidation flows can cause slippage that mis-prices the collateral seized versus debt repaid. |
| Spokes do not return minted/burned share amounts | Core Spoke functions don’t return minted/burned share amounts, forcing integrators to recompute them and risking inconsistencies. |
| Unnecessary check and parameter in calculateVariableLiquidationBonus() | calculateVariableLiquidationBonus() takes an unnecessary parameter and performs redundant checks that don’t change outcomes. |
| Unnecessary check in _notifyRiskPremiumUpdate | _notifyRiskPremiumUpdate includes a superfluous check that can be removed to simplify control flow and save gas. |
| It might make sense to first liquidate debt than collateral | Liquidation is implemented as “collateral first, debt later,” but reversing the order could give better health factor dynamics in some scenarios. |
| Auto-disable collateral when user’s balance is depleted | Reserves remain marked “using as collateral” even with zero balance; auto-disabling would simplify HF and UX. |
| Spoke should validate new oracle is compatible with existing reserves | When changing the price oracle, Spoke doesn’t verify compatibility (e.g. decimals), risking incorrect valuations. |
| maxLiquidationBonus config should always be > PercentageMath.PERCENTAGE_FACTOR | maxLiquidationBonus must always be strictly greater than the base percentage factor; otherwise configs make no sense. |
| Users should not enable collateral on reserves where they have no supply | Users can enable a reserve as collateral even if they’ve supplied no balance, which is pointless and confusing. |
| Hub._validateAdd should include accrued-but-unminted fees in addCap validation | _validateAdd enforces addCap using raw asset amounts instead of cap-consistent values, mis-measuring usage. |
| Users can repay drawnAmount > 0 without reducing any debt shares | Users can repay a positive drawn amount but leave debt shares unchanged, effectively paying premium-only and leaving base debt untouched. |
| reinvestmentController behavior should follow same limitations as Spoke | The reinvestment controller bypasses some of the constraints applied to Spokes, creating asymmetric risk. |
| Hub._validateDraw uses wrong value for drawCap check | _validateDraw compares the wrong value to drawCap, potentially allowing over- or under-lending vs configuration. |
| SpokeInstance initialization should be pushed down into Spoke base | SpokeInstance initialization logic is duplicated and should live in the base Spoke to reduce mistakes. |
| Updating collateralFactor to 0 can prevent valid liquidations | Setting a reserve’s collateral factor to 0 can prevent valid liquidations that should otherwise be allowed. |
| collateralFactor = 0 can create incoherent userRiskPremium | A collateral factor of 0 interacts badly with userRiskPremium math, producing incoherent premium values. |
| Users with existing supply cannot newly enable frozen reserves as collateral to improve HF | Once a reserve is frozen, users with existing supply can’t newly enable it as collateral to improve their health factor. |
| Check against addCap upper bound ignores accrued-but-unrealized fees (post-fees refactor) | Updated addCap validation ignores accrued but unrealized fees, allowing effective adds above the intended cap. |
| Lack of Chainlink Oracle staleness check | Oracle wrapper uses latestRoundData but ignores updatedAt, so stale Chainlink prices could be used without any freshness guard. |
| Undocumented cascading risks on inactive spoke | Deactivating a Spoke can break other Hub/Spoke operations that depend on it, creating cascading operational risks. |
| Spoke contract does not follow upgradeability best practices | Spoke’s upgradeable storage layout doesn’t follow ERC‑7201 / structured-slot best practices, risking storage clashes on upgrade. |
| Incorrect liquidation event documentation | Liquidation event docs don’t match the actual emitted parameters, causing off-chain consumers to misinterpret events. |
| The setSelfAsUserPositionManagerWithSig function assumes the positionManager value in the params argument is address(this) | setSelfAsUserPositionManagerWithSig assumes positionManager == address(this) without enforcing it, allowing malformed signatures. |
| Ambiguous revert reasons | Generic revert messages and errors make it hard to distinguish failure causes and complicate monitoring. |
| Code duplication | Logic is duplicated across modules, increasing maintenance overhead and the risk of inconsistent fixes. |
| Hanging approval in SignatureGateway | SignatureGateway can end up with approvals that are never consumed, leaving “hanging” allowances and odd UX. |
| Inconsistent variable names | Variable names are inconsistent across contracts, harming readability and slowing down security review. |
| Listed Spoke cannot be set as fee receiver | Some configurations prevent a listed Spoke from being used as a fee receiver, limiting flexibility vs stated design. |
| Low granularity in drawCap and addCap | drawCap and addCap use coarse units, limiting precision when tuning risk per asset/spoke. |
| No on-chain reverse mapping for reserveId | There is no on-chain reverse mapping from reserveId to underlying/hub, making integrations rely on off-chain metadata. |
| preview* and convert* return 1:1 exchange rate for unsupported assets | preview*/convert* helpers default to a 1:1 rate for unsupported assets, which can mislead callers that don’t validate support. |
| Non-functional unchecked usage | unchecked blocks are used where overflow is already impossible, hurting clarity without gas benefit. |
| PercentageMath naming is inaccurate | PercentageMath names/units don’t align perfectly with basis-point semantics, which can confuse users of the library. |
| Overly narrow documentation of WadRayMath functions | WadRayMath documentation understates valid ranges and rounding behavior, making correct usage less obvious. |
| Liquidation gas cost is possibly more than reward | In some configurations the liquidation incentive may be lower than gas costs, disincentivizing liquidators. |
| Paused assets keep accruing interest | Interest continues to accrue on paused reserves, which may be surprising and can impact borrowers during pauses. |
| Tokens with hooks are not supported | The system doesn’t support tokens with hooks/fees/rebasing; using them can break assumptions. |
| TreasurySpoke.supply should probably revert | TreasurySpoke.supply shouldn’t be usable; reverting there would prevent semantically meaningless calls. |
| Add explicit documentation for deficit elimination | The deficit elimination mechanism should be clearly documented so stakeholders understand who pays for deficits. |
| Improve events emitted by restricted functions | Functions protected by restricted should emit richer events to make admin actions traceable. |
| Spoke.addAsset should revert if spoke is disabled/zero-cap/not configured on Hub | Spoke.addAsset should revert when the Spoke is disabled, caps are zero, or the asset isn’t configured in the Hub. |
| Permit feature should ideally be tied to a core action | Permit-based flows should be restricted to core actions to reduce attack surface and complexity. |
| permitReserve should revert if follow-up action cannot execute | permitReserve should revert if the follow-up operation would fail, so signatures aren’t consumed for no effect. |
| isUsingAsCollateral should document collateralFactor = 0 semantics | Natspec for isUsingAsCollateral should explicitly explain behavior when collateralFactor = 0. |
| Spoke.getLiquidationBonus function name is misleading | getLiquidationBonus’s name doesn’t reflect exactly what it returns and can mislead integrators. |
| Consider forcefully resetting all borrower debt in deficit scenario | In a deficit scenario, forcibly resetting borrower debt might be preferable to leaving odd residual positions. |
| avgCollateralFactor in account data is in WAD, not BPS | _calculateAndPotentiallyRefreshUserAccountData returns avgCollateralFactor in WAD units despite docs implying BPS. |
| Users should not enable-as-collateral reserves with collateralFactor = 0 | Users should not be able to enable-as-collateral reserves whose collateralFactor is zero. |
| When collateralFactor = 0, maxLiquidationBonus and liquidationFee should be 0 | When collateralFactor = 0, both maxLiquidationBonus and liquidationFee should also be zero. |
| Frozen reserves already enabled as collateral still contribute to borrowing power | Frozen reserves that were previously enabled as collateral still contribute to HF and borrowing power. |
| Freezing/pausing asset on Hub does not affect reinvestmentController behavior | Freezing/pausing an asset in the Hub doesn’t affect the reinvestment controller, which can keep moving that liquidity. |
| Possible OOG on HubConfigurator bulk actions | HubConfigurator bulk operations across many assets risk out-of-gas reverts. |
| SpokeNotActive error is misleading when reverting for non-listed Spoke | SpokeNotActive error conflates “not active” with “not listed,” which is misleading. |
| Custom error set can be improved | Some custom errors are missing or inconsistently named; tightening them improves clarity. |
| Need documentation for why Hub can list multiple instances of same underlying | Documentation should justify why multiple Hub assets for the same underlying token are allowed and how to reason about them. |
| reinvestmentController has no cap on liquidity it can pull from Hub | Reinvestment controller has no explicit cap on how much liquidity it can pull from the Hub, concentrating risk. |
| Hub.addAsset should accept liquidityFee parameter | Hub.addAsset should expose liquidityFee as an input parameter to avoid misconfigured defaults. |
| Behavior and logic of reinvestmentController should be better documented | The reinvestment controller’s behavior and assumptions should be clearly documented. |
| Ensure feeReceiver (TreasurySpoke) is bound to correct Hub | Per-borrower dynamic config snapshots can desync from the current reserve config, leading to surprising behavior. |
| Document all rounding decisions | All rounding decisions across the math should be explicitly documented for easier auditing and porting. |
| Add extra sanity checks to Interest Rate Strategy at Hub level | Additional sanity checks for interest-rate parameters should be enforced at the Hub level. |
| Spoke nonce management and usage can be improved | Spoke nonce management could be improved to make replay protection behavior clearer and more robust. |
| Considerations on liquidation rounding directions | Tiny liquidations can be over-incentivized, letting liquidators extract excess collateral and further degrade borrower HF. |
| Use of Spoke deficit in drawCap validation should be documented | The way Spoke deficit is incorporated into draw-cap validation should be clearly documented for governance and integrators. |
| Misc informational / documentation / natspec issues (fix review bundle) | Fix-review introduced additional minor doc/natspec informational issues that should be cleaned up. |
| Liquidations with receiveShares = true won’t revert when asset/spoke paused and liquidationFee == 0 | With receiveShares = true and liquidationFee = 0, liquidations may proceed on paused (asset, Spoke) pairs instead of reverting. |
| _liquidateCollateral should revert when liquidator receives zero shares and receiveShares = true | _liquidateCollateral should revert when computed share amount is zero while receiveShares = true, since such a liquidation is pointless. |
| Considerations relative to new upper bounds for addCap and drawCap | The new upper bound logic for addCap and drawCap introduces nuances that need explicit reasoning and explanation. |
| FeeReceiver behavior differs between _mintFeeShares and payFeeShares | _mintFeeShares and payFeeShares validate feeReceiver differently, leading to inconsistent behavior. |
| New feeReceiver may receive old feeReceiver’s not-yet-minted fee shares | Changing feeReceiver while old fees are not yet minted can result in the new receiver inheriting those pending fee shares. |
| Debt Asset Risk Discrepancy | The risk and premium are only applied to the collateral, which can lead to a user paying a high premium for risky collateral against safe debt, or a low/no premium for safe collateral against risky debt | 
| Paused peripheral asset blocks liquidations that involve deficit | Currently, if a spoke is paused for a peripheral asset involved in liquidations (ie not directly repaid or seized in the liquidation, but merely another debt asset on the liquidated user position), it does not block the liquidation from going through in normal liquidation flows, because `hub.refreshPremium` can still be called by paused spokes. However, liquidations that involve deficit reporting will still be blocked by paused spokes, because in `hub.reportDeficit`, the paused flag is still validated. Thus, the behaviour of paused peripheral assets is inconsistent in liquidations. |
| Interest Accrual Compounding Effect | Triggering interest accrual on every block can lead to an actual interest rate up to 2,000x greater than intended due to the compounding effect | 
| Liquidation DOS Frontrunning | Liquidations can be frontrun by providing collateral or repaying a small share of the debt, triggering the dust amounts check | 
| Insignificant Liquidation Frontrunning | Liquidations can be frontrun with another liquidation or repay action, forcing it to liquidate only a small, insignificant amount of debt | 
| Irreversible feeReceiver Change	| Attempting to revert to the initial feeReceiver will cause a revert because it is already a spoke | 
| Hub asset `inactive` inconsistency in liquidations involving peripheral debt asset | - in typical cases, if the peripheral debt asset is inactive, the liquidation will revert on `hub.refreshPremium`; - however, if the peripheral debt asset is inactive, AND rp goes from 0 -> 0 the liquidation will not be blocked; - when rp 0 -> 0, `hub.refreshPremium` is skipped |
| Hub asset `inactive` inconsistency in liquidations involving primary collateral asset | - in typical cases, if the primary collateral asset is `inactive`, the liquidation will revert on hub actions remove, payFeeShares, or refreshPremium; - however, if the primary collateral asset is `inactive`, AND the liquidator has opted to `receiveShares` AND the amount of `feeShares` is 0, AND rp 0 -> 0 the liquidation will not be blocked; - this is because during a `receiveShares` liquidation, the `hub.remove` is skipped, and thus cannot revert on an inactive spoke. Similarly, with 0 feeShares, the `hub.payFeeShares` action is skipped, and thus cannot revert on an inactive spoke. And when rp 0 -> 0, `hub.refreshPremium` is skipped; - 0 fee shares can occur when either liquidationFee is set to 0, or fee shares rounds to 0 on a small liquidation |
| Hub.eliminateDeficit validations are not consistent with other functions of the Hub | Hub.eliminateDeficit only checks that the caller Spoke is active. It does not check whether the caller is paused, and it does not check any status flags of the covered Spoke. It could make sense not to check the covered Spoke’s flags, since the covered Spoke may be in a bad or paused state, and we still want to allow clearing its bad debt. For the caller Spoke, checking active is appropriate because it is the general switch for the Spoke. However, it also makes sense to check the paused flag, since eliminating a deficit reduces the caller Spoke’s addedShares, which is equivalent to a remove operation (and remove enforce the paused flag). |
