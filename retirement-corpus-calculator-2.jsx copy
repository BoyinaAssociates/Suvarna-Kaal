import React, { useMemo, useState } from "react";
import {
  AreaChart,
  Area,
  BarChart,
  Bar,
  XAxis,
  YAxis,
  CartesianGrid,
  Tooltip,
  Legend,
  ResponsiveContainer,
} from "recharts";
import {
  TrendingUp,
  PiggyBank,
  Landmark,
  ArrowRightLeft,
  Info,
  Plus,
  Trash2,
  Wallet,
  Sparkles,
  Gift,
} from "lucide-react";

/* ---------------------------------------------------------------
   THE LEDGER — a retirement corpus calculator styled like an old
   bank passbook: ink-navy covers, parchment statement pages,
   brass foil numerals, and three "jars" standing in for the
   liquid / balanced / growth buckets of a retirement portfolio.
------------------------------------------------------------------*/

const C = {
  ink: "#0F1A2B",
  ink2: "#16233A",
  inkLine: "#2A3A54",
  parchment: "#F4EFE2",
  parchment2: "#ECE4D2",
  parchmentLine: "#D8CCAE",
  brass: "#BD8A3D",
  brassLight: "#E3BD7C",
  ink3: "#1C2C46",
  textInk: "#1C2233",
  textMuted: "#7E8CA6",
  teal: "#2F6F62",
  tealLight: "#6FA895",
  rust: "#B24B3D",
  rustLight: "#D98A78",
  slate: "#4C6B8A",
  slateLight: "#93AECB",
  gold2: "#8E6A2E",
  plum: "#6B4C7A",
  plumLight: "#A987B8",
};

const fontImport = `
@import url('https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,400;9..144,500;9..144,600;9..144,700&family=Inter:wght@400;500;600;700&family=IBM+Plex+Mono:wght@400;500;600&display=swap');
`;

const inr = (n, opts = {}) => {
  if (!isFinite(n)) return "—";
  return new Intl.NumberFormat("en-IN", {
    style: "currency",
    currency: "INR",
    maximumFractionDigits: opts.decimals ?? 0,
    minimumFractionDigits: opts.decimals ?? 0,
  }).format(n);
};

const shortInr = (n) => {
  if (!isFinite(n)) return "—";
  const sign = n < 0 ? "-" : "";
  const abs = Math.abs(n);
  if (abs >= 1e7) return `${sign}₹${(abs / 1e7).toFixed(2)} Cr`;
  if (abs >= 1e5) return `${sign}₹${(abs / 1e5).toFixed(2)} L`;
  if (abs >= 1e3) return `${sign}₹${(abs / 1e3).toFixed(1)} K`;
  return `${sign}₹${abs.toFixed(0)}`;
};

const monthlyRateOf = (annualPct) => Math.pow(1 + annualPct / 100, 1 / 12) - 1;

function NumberField({ label, value, onChange, suffix, step = 1, min = 0, hint, compact }) {
  return (
    <label className="flex flex-col gap-1">
      {label && (
        <span
          className="text-xs tracking-wide uppercase flex items-center gap-1"
          style={{ color: C.textMuted, fontFamily: "Inter, sans-serif", letterSpacing: "0.06em" }}
        >
          {label}
        </span>
      )}
      <div
        className="flex items-center rounded-md overflow-hidden border"
        style={{ borderColor: C.parchmentLine, background: "#fff" }}
      >
        <input
          type="number"
          value={value}
          step={step}
          min={min}
          onChange={(e) => onChange(e.target.value === "" ? 0 : parseFloat(e.target.value))}
          className={`w-full outline-none bg-transparent ${compact ? "px-2 py-1.5" : "px-3 py-2"}`}
          style={{
            fontFamily: "IBM Plex Mono, monospace",
            color: C.textInk,
            fontSize: compact ? "13px" : "15px",
          }}
        />
        {suffix && (
          <span
            className="px-2 text-xs"
            style={{ color: C.textMuted, fontFamily: "Inter, sans-serif", borderLeft: `1px solid ${C.parchmentLine}` }}
          >
            {suffix}
          </span>
        )}
      </div>
      {hint && (
        <span style={{ color: C.textMuted, fontSize: "11px", fontFamily: "Inter, sans-serif" }}>{hint}</span>
      )}
    </label>
  );
}

function SectionLabel({ n, title, dark }) {
  return (
    <div className="flex items-baseline gap-3 mb-4">
      <span style={{ fontFamily: "Fraunces, serif", color: C.brass, fontSize: "13px", letterSpacing: "0.1em" }}>
        {n}
      </span>
      <h3
        style={{
          fontFamily: "Fraunces, serif",
          color: dark ? C.parchment : C.textInk,
          fontSize: "18px",
          fontWeight: 600,
        }}
      >
        {title}
      </h3>
      <div className="flex-1" style={{ borderBottom: `1px solid ${dark ? C.inkLine : C.parchmentLine}` }} />
    </div>
  );
}

// Glass jar visual for a bucket
function Jar({ label, sub, amount, pct, color, colorLight }) {
  const height = 150;
  const width = 96;
  const fillH = Math.max(4, (Math.min(100, pct) / 100) * (height - 18));
  const fillY = height - 10 - fillH;
  return (
    <div className="flex flex-col items-center gap-3">
      <svg width={width} height={height} viewBox={`0 0 ${width} ${height}`}>
        <defs>
          <linearGradient id={`grad-${label}`} x1="0" y1="0" x2="0" y2="1">
            <stop offset="0%" stopColor={colorLight} />
            <stop offset="100%" stopColor={color} />
          </linearGradient>
          <clipPath id={`clip-${label}`}>
            <rect x="6" y="8" width={width - 12} height={height - 16} rx="10" />
          </clipPath>
        </defs>
        <rect
          x="6"
          y="8"
          width={width - 12}
          height={height - 16}
          rx="10"
          fill="rgba(255,255,255,0.5)"
          stroke={C.parchmentLine}
          strokeWidth="2"
        />
        <g clipPath={`url(#clip-${label})`}>
          <rect x="6" y={fillY} width={width - 12} height={fillH + 20} fill={`url(#grad-${label})`} />
          <ellipse cx={width / 2} cy={fillY} rx={(width - 12) / 2} ry="4" fill={colorLight} opacity="0.8" />
        </g>
        <rect x="14" y="4" width={width - 28} height="8" rx="3" fill={C.parchmentLine} />
      </svg>
      <div className="text-center">
        <div style={{ fontFamily: "Fraunces, serif", fontWeight: 600, color: C.textInk, fontSize: "15px" }}>
          {label}
        </div>
        <div style={{ fontFamily: "Inter, sans-serif", color: C.textMuted, fontSize: "11px" }}>{sub}</div>
        <div
          style={{
            fontFamily: "IBM Plex Mono, monospace",
            color: color,
            fontSize: "14px",
            fontWeight: 600,
            marginTop: "4px",
          }}
        >
          {shortInr(amount)}
        </div>
        <div style={{ fontFamily: "Inter, sans-serif", color: C.textMuted, fontSize: "11px" }}>
          {pct.toFixed(0)}% of corpus
        </div>
      </div>
    </div>
  );
}

let invUid = 4;
let polUid = 3;

export default function RetirementLedger() {
  const [inputs, setInputs] = useState({
    currentAge: 30,
    retirementAge: 60,
    lifeExpectancy: 85,
    expenseMode: "monthly",
    expense: 50000,
    preInflation: 7,
    postInflation: 6,
    preReturn: 12,
    postReturn: 8,
    bucket1Years: 3,
    bucket2Years: 7,
    bucket1Return: 6,
    bucket2Return: 9,
    bucket3Return: 12,
    estateBequest: 0,
  });

  const [investments, setInvestments] = useState([
    { id: 1, name: "EPF / PF", amount: 0, returnRate: 8.25, monthly: 0 },
    { id: 2, name: "NPS", amount: 0, returnRate: 10, monthly: 0 },
    { id: 3, name: "PPF", amount: 0, returnRate: 7.1, monthly: 0 },
  ]);

  const [policies, setPolicies] = useState([
    { id: 1, name: "LIC Policy 1", amount: 0, maturityAge: 50, reinvestReturn: 7 },
    { id: 2, name: "LIC Policy 2", amount: 0, maturityAge: 58, reinvestReturn: 7 },
  ]);

  const set = (key) => (val) => setInputs((s) => ({ ...s, [key]: val }));

  const updateInvestment = (id, field, val) =>
    setInvestments((list) => list.map((inv) => (inv.id === id ? { ...inv, [field]: val } : inv)));
  const addInvestment = () => {
    invUid += 1;
    setInvestments((list) => [...list, { id: invUid, name: "Other", amount: 0, returnRate: 8, monthly: 0 }]);
  };
  const removeInvestment = (id) => setInvestments((list) => list.filter((inv) => inv.id !== id));

  const updatePolicy = (id, field, val) =>
    setPolicies((list) => list.map((p) => (p.id === id ? { ...p, [field]: val } : p)));
  const addPolicy = () => {
    polUid += 1;
    setPolicies((list) => [
      ...list,
      { id: polUid, name: `LIC Policy ${polUid}`, amount: 0, maturityAge: inputs.retirementAge, reinvestReturn: 7 },
    ]);
  };
  const removePolicy = (id) => setPolicies((list) => list.filter((p) => p.id !== id));

  const calc = useMemo(() => {
    const {
      currentAge,
      retirementAge,
      lifeExpectancy,
      expenseMode,
      expense,
      preInflation,
      postInflation,
      preReturn,
      postReturn,
      bucket1Years,
      bucket2Years,
      bucket1Return,
      bucket2Return,
      bucket3Return,
      estateBequest,
    } = inputs;

    const yearsToRetirement = Math.max(0, retirementAge - currentAge);
    const retirementYears = Math.max(0, lifeExpectancy - retirementAge);
    const annualExpenseToday = expenseMode === "monthly" ? expense * 12 : expense;
    const months = yearsToRetirement * 12;

    // Step 1: future value of expense at retirement (pre-retirement inflation)
    const futureAnnualExpenseYear1 =
      annualExpenseToday * Math.pow(1 + preInflation / 100, yearsToRetirement);

    // Step 2: real rate of return post retirement (Fisher equation) — used only
    // for the single-blended-rate "quick estimate" shown as a cross-check.
    const realRate = (1 + postReturn / 100) / (1 + postInflation / 100) - 1;

    // Yearly expense path through retirement (each year's expense grows on the
    // PREVIOUS year's, compounding — not a linear/simple increase).
    const expenses = [];
    for (let y = 0; y < retirementYears; y++) {
      expenses.push(futureAnnualExpenseYear1 * Math.pow(1 + postInflation / 100, y));
    }

    // Legacy / estate you want left for your kids at the end of life. Grown from
    // today's value at pre-retirement inflation, then post-retirement inflation,
    // to its future value at death (compounding, not linear).
    let legacyFVatDeath = 0;
    if (estateBequest > 0) {
      legacyFVatDeath =
        estateBequest *
        Math.pow(1 + preInflation / 100, yearsToRetirement) *
        Math.pow(1 + postInflation / 100, retirementYears);
    }

    // --- QUICK ESTIMATE (single blended post-retirement rate, for cross-checking only) ---
    let corpusQuickEstimate = 0;
    if (retirementYears > 0) {
      if (Math.abs(realRate) < 1e-9) {
        corpusQuickEstimate = futureAnnualExpenseYear1 * retirementYears;
      } else {
        corpusQuickEstimate =
          (futureAnnualExpenseYear1 * (1 - Math.pow(1 + realRate, -retirementYears))) /
          realRate *
          (1 + realRate);
      }
    }
    const legacyPVQuick =
      estateBequest > 0
        ? retirementYears > 0
          ? legacyFVatDeath / Math.pow(1 + postReturn / 100, retirementYears)
          : legacyFVatDeath
        : 0;
    corpusQuickEstimate += legacyPVQuick;

    // --- BUCKET-BASED CORPUS (the number that actually drives the SIP) ---
    // Each bucket is discounted back to the retirement date (day 0) using its
    // OWN rate — not the blended average — because that's the rate the money in
    // that bucket will actually compound at while it waits to be spent. This
    // matters: Bucket 2/3 money is invested from day 1 of retirement even though
    // it isn't withdrawn until later years, so it must be discounted the full
    // number of years back to today, not just from the start of its own window.
    const b1Years = Math.min(bucket1Years, retirementYears);
    const b2Years = Math.min(bucket2Years, Math.max(0, retirementYears - b1Years));
    const b3Years = Math.max(0, retirementYears - b1Years - b2Years);

    let bucket1Target = 0;
    for (let y = 0; y < b1Years; y++) {
      bucket1Target += expenses[y] / Math.pow(1 + bucket1Return / 100, y);
    }
    let bucket2Target = 0;
    for (let y = b1Years; y < b1Years + b2Years; y++) {
      bucket2Target += expenses[y] / Math.pow(1 + bucket2Return / 100, y); // full exponent from day 0
    }
    let bucket3ExpensePV = 0;
    for (let y = b1Years + b2Years; y < retirementYears; y++) {
      bucket3ExpensePV += expenses[y] / Math.pow(1 + bucket3Return / 100, y); // full exponent from day 0
    }
    // Legacy sits in Bucket 3, so it's discounted at Bucket 3's own rate.
    const legacyPVatRetirement =
      estateBequest > 0
        ? retirementYears > 0
          ? legacyFVatDeath / Math.pow(1 + bucket3Return / 100, retirementYears)
          : legacyFVatDeath
        : 0;
    const bucket3Target = bucket3ExpensePV + legacyPVatRetirement;

    // Total corpus required = sum of the three buckets, each valued at its own
    // rate. This is the figure used everywhere below (SIP, coverage, etc).
    const corpus = bucket1Target + bucket2Target + bucket3Target;

    // Step 4: project existing investments (PF/NPS/PPF/other) forward to retirement.
    const invSims = investments.map((inv) => ({
      ...inv,
      bal: inv.amount,
      mRate: monthlyRateOf(inv.returnRate),
    }));

    // Step 4b: classify each LIC / maturity policy —
    //  - matures before today: already in hand, compounds from now
    //  - matures on/before retirement: reinvested until retirement
    //  - matures after retirement: paid out mid-retirement, tops up Bucket 3 that year
    const policySetup = policies
      .filter((p) => p.amount > 0)
      .map((p) => {
        if (p.maturityAge <= currentAge) return { ...p, mode: "preexisting" };
        if (p.maturityAge <= retirementAge) {
          const injectMonth = Math.max(
            1,
            Math.min(Math.max(months, 1), Math.round((p.maturityAge - currentAge) * 12))
          );
          return { ...p, mode: "pre", injectMonth };
        }
        return { ...p, mode: "during" };
      });

    const preRetPolicySims = policySetup
      .filter((p) => p.mode !== "during")
      .map((p) => ({ ...p, bal: p.mode === "preexisting" ? p.amount : 0, mRate: monthlyRateOf(p.reinvestReturn) }));
    const duringRetirementPolicies = policySetup.filter((p) => p.mode === "during");

    const existingTrajectory = [];
    for (let m = 1; m <= months; m++) {
      invSims.forEach((inv) => {
        inv.bal = (inv.bal + inv.monthly) * (1 + inv.mRate);
      });
      preRetPolicySims.forEach((p) => {
        p.bal = (p.bal + (m === p.injectMonth ? p.amount : 0)) * (1 + p.mRate);
      });
      if (m % 12 === 0) {
        existingTrajectory.push({
          age: currentAge + m / 12,
          existing: invSims.reduce((s, inv) => s + inv.bal, 0) + preRetPolicySims.reduce((s, p) => s + p.bal, 0),
        });
      }
    }
    const investTotal =
      months > 0 ? invSims.reduce((s, inv) => s + inv.bal, 0) : investments.reduce((s, inv) => s + inv.amount, 0);
    const policyTotal =
      months > 0
        ? preRetPolicySims.reduce((s, p) => s + p.bal, 0)
        : policySetup.filter((p) => p.mode === "preexisting").reduce((s, p) => s + p.amount, 0);
    const alreadyCovered = investTotal + policyTotal;

    // Step 5: additional corpus still needed, and the revised SIP for it
    const additionalCorpusNeeded = Math.max(0, corpus - alreadyCovered);
    const surplus = Math.max(0, alreadyCovered - corpus);

    const monthlyReturn = monthlyRateOf(preReturn);
    let sip = 0;
    if (months > 0 && additionalCorpusNeeded > 0) {
      if (Math.abs(monthlyReturn) < 1e-9) {
        sip = additionalCorpusNeeded / months;
      } else {
        sip =
          (additionalCorpusNeeded * monthlyReturn) /
          ((Math.pow(1 + monthlyReturn, months) - 1) * (1 + monthlyReturn));
      }
    }

    // Combined accumulation trajectory: existing investments + policies + new SIP
    const accumulation = [];
    let sipBal = 0;
    for (let m = 1; m <= months; m++) {
      sipBal = (sipBal + sip) * (1 + monthlyReturn);
      if (m % 12 === 0) {
        const existingPoint = existingTrajectory[m / 12 - 1]?.existing ?? 0;
        accumulation.push({
          age: currentAge + m / 12,
          existing: Math.round(existingPoint),
          newSip: Math.round(sipBal),
          total: Math.round(existingPoint + sipBal),
        });
      }
    }

    // Year-by-year retirement simulation with rebalancing.
    // Any LIC policy maturing during retirement lands in Bucket 3 that year.
    let b1 = bucket1Target;
    let b2 = bucket2Target;
    let b3 = bucket3Target;
    const rows = [];
    for (let y = 0; y < retirementYears; y++) {
      const age = retirementAge + y;
      const exp = expenses[y];

      const licInjection = duringRetirementPolicies
        .filter((p) => p.maturityAge === age)
        .reduce((s, p) => s + p.amount, 0);
      b3 += licInjection;

      let shortfall = 0;
      b1 -= exp;
      if (b1 < 0) {
        shortfall = -b1;
        b1 = 0;
        const pull = Math.min(shortfall, b2);
        b2 -= pull;
        shortfall -= pull;
      }

      b1 *= 1 + bucket1Return / 100;
      b2 *= 1 + bucket2Return / 100;
      b3 *= 1 + bucket3Return / 100;

      const nextExp = expenses[Math.min(y + 1, retirementYears - 1)] || exp;
      const targetB1 = nextExp * b1Years;
      const targetB2 = nextExp * b2Years;

      let transferB2toB1 = Math.max(0, targetB1 - b1);
      transferB2toB1 = Math.min(transferB2toB1, b2);
      b1 += transferB2toB1;
      b2 -= transferB2toB1;

      let transferB3toB2 = Math.max(0, targetB2 - b2);
      transferB3toB2 = Math.min(transferB3toB2, b3);
      b2 += transferB3toB2;
      b3 -= transferB3toB2;

      rows.push({
        year: y + 1,
        age,
        expense: exp,
        withdrawShortfall: shortfall,
        licInjection,
        b1: Math.max(0, b1),
        b2: Math.max(0, b2),
        b3: Math.max(0, b3),
        total: Math.max(0, b1) + Math.max(0, b2) + Math.max(0, b3),
        transferB2toB1,
        transferB3toB2,
      });
    }

    const depleted = rows.find((r) => r.total <= 0 && r.withdrawShortfall > 0);
    const projectedLegacy = rows.length ? rows[rows.length - 1].total : alreadyCovered;

    return {
      yearsToRetirement,
      retirementYears,
      futureAnnualExpenseYear1,
      futureMonthlyExpenseYear1: futureAnnualExpenseYear1 / 12,
      realRate,
      corpusQuickEstimate,
      legacyFVatDeath,
      legacyPVatRetirement,
      corpus,
      alreadyCovered,
      additionalCorpusNeeded,
      surplus,
      sip,
      accumulation,
      bucket1Target,
      bucket2Target,
      bucket3Target,
      b1Years,
      b2Years,
      b3Years,
      rows,
      depleted,
      projectedLegacy,
    };
  }, [inputs, investments, policies]);

  const bucketTotal = calc.bucket1Target + calc.bucket2Target + calc.bucket3Target || 1;

  return (
    <div style={{ background: C.ink, minHeight: "100%", fontFamily: "Inter, sans-serif" }}>
      <style>{fontImport}</style>

      {/* Header / cover */}
      <div
        className="px-6 py-10 md:px-12"
        style={{
          background: `linear-gradient(160deg, ${C.ink} 0%, ${C.ink2} 100%)`,
          borderBottom: `1px solid ${C.inkLine}`,
        }}
      >
        <div className="max-w-5xl mx-auto">
          <div className="flex items-center gap-2 mb-2">
            <div style={{ width: 28, height: 2, background: C.brass }} />
            <span
              style={{
                color: C.brass,
                fontSize: "12px",
                letterSpacing: "0.2em",
                textTransform: "uppercase",
                fontFamily: "Inter, sans-serif",
              }}
            >
              A Retirement Passbook
            </span>
          </div>
          <h1
            style={{
              fontFamily: "Fraunces, serif",
              color: C.parchment,
              fontSize: "clamp(28px, 4vw, 42px)",
              fontWeight: 600,
              lineHeight: 1.1,
            }}
          >
            The Retirement Corpus Ledger
          </h1>
          <p style={{ color: C.textMuted, marginTop: "10px", maxWidth: "600px", fontSize: "14.5px" }}>
            Enter today's expenses, your working years, and expected rates. The ledger
            nets off what your investments and LIC policies are already building towards,
            adds room for what you want to leave behind, and works out the additional
            monthly SIP that closes the gap.
          </p>
        </div>
      </div>

      {/* Inputs */}
      <div className="max-w-5xl mx-auto px-6 md:px-12 -mt-6">
        <div
          className="rounded-xl p-6 md:p-8 shadow-2xl"
          style={{ background: C.parchment, border: `1px solid ${C.parchmentLine}` }}
        >
          <SectionLabel n="01" title="Your Timeline & Expenses" />
          <div className="grid grid-cols-2 md:grid-cols-4 gap-4 mb-8">
            <NumberField label="Current Age" value={inputs.currentAge} onChange={set("currentAge")} suffix="yrs" />
            <NumberField
              label="Retirement Age"
              value={inputs.retirementAge}
              onChange={set("retirementAge")}
              suffix="yrs"
            />
            <NumberField
              label="Life Expectancy"
              value={inputs.lifeExpectancy}
              onChange={set("lifeExpectancy")}
              suffix="yrs"
            />
            <label className="flex flex-col gap-1">
              <span
                className="text-xs tracking-wide uppercase"
                style={{ color: C.textMuted, letterSpacing: "0.06em" }}
              >
                Expense Frequency
              </span>
              <div className="flex rounded-md overflow-hidden border" style={{ borderColor: C.parchmentLine }}>
                {["monthly", "yearly"].map((m) => (
                  <button
                    key={m}
                    onClick={() => set("expenseMode")(m)}
                    className="flex-1 py-2 text-sm capitalize"
                    style={{
                      background: inputs.expenseMode === m ? C.brass : "#fff",
                      color: inputs.expenseMode === m ? "#fff" : C.textInk,
                      fontFamily: "Inter, sans-serif",
                    }}
                  >
                    {m}
                  </button>
                ))}
              </div>
            </label>
            <NumberField
              label={`Current ${inputs.expenseMode === "monthly" ? "Monthly" : "Yearly"} Expense`}
              value={inputs.expense}
              onChange={set("expense")}
              suffix="₹"
              step={1000}
            />
          </div>

          <SectionLabel n="02" title="Inflation & Return Assumptions" />
          <div className="grid grid-cols-2 md:grid-cols-4 gap-4 mb-8">
            <NumberField
              label="Pre-Retirement Inflation"
              value={inputs.preInflation}
              onChange={set("preInflation")}
              suffix="%"
              step={0.5}
              hint="Grows today's expense to retirement"
            />
            <NumberField
              label="Post-Retirement Inflation"
              value={inputs.postInflation}
              onChange={set("postInflation")}
              suffix="%"
              step={0.5}
              hint="Grows expenses during retirement"
            />
            <NumberField
              label="Pre-Retirement Return"
              value={inputs.preReturn}
              onChange={set("preReturn")}
              suffix="%"
              step={0.5}
              hint="Used to size the new monthly SIP"
            />
            <NumberField
              label="Post-Retirement Return"
              value={inputs.postReturn}
              onChange={set("postReturn")}
              suffix="%"
              step={0.5}
              hint="Blended portfolio return, sets real rate"
            />
          </div>

          {/* Existing investments */}
          <SectionLabel n="03" title="Existing Investments" />
          <div className="flex items-start gap-2 mb-4" style={{ color: C.textMuted, fontSize: "12px" }}>
            <Info size={14} style={{ marginTop: "2px", flexShrink: 0 }} />
            <span>
              Add every ongoing investment — PF, NPS, PPF, mutual funds, and so on — with its
              current balance, expected return, and any monthly contribution that will continue
              until retirement. Rename rows as needed.
            </span>
          </div>
          <InvestmentTable
            rows={investments}
            onUpdate={updateInvestment}
            onRemove={removeInvestment}
            columns={["Name", "Current Balance", "Return", "Monthly Contribution"]}
          />
          <button
            onClick={addInvestment}
            className="flex items-center gap-1.5 text-sm rounded-md px-3 py-2 mt-1"
            style={{ color: C.gold2, border: `1px dashed ${C.brass}`, fontFamily: "Inter, sans-serif" }}
          >
            <Plus size={14} /> Add investment
          </button>

          {/* LIC / maturity policies — multiple */}
          <div className="mt-8">
            <SectionLabel n="04" title="LIC / Maturity Policies" />
            <div className="flex items-start gap-2 mb-4" style={{ color: C.textMuted, fontSize: "12px" }}>
              <Info size={14} style={{ marginTop: "2px", flexShrink: 0 }} />
              <span>
                Add one row per policy — clients often hold several, maturing in different years.
                A policy maturing on or before retirement is reinvested until retirement; one
                maturing later pays out mid-retirement and tops up Bucket 3 that year.
              </span>
            </div>
            <div className="flex flex-col gap-3">
              <div
                className="hidden md:grid gap-3 px-1"
                style={{ gridTemplateColumns: "1.1fr 1fr 0.8fr 1fr 32px", color: C.textMuted, fontSize: "11px" }}
              >
                <span className="uppercase" style={{ letterSpacing: "0.06em" }}>
                  Policy Name
                </span>
                <span className="uppercase" style={{ letterSpacing: "0.06em" }}>
                  Maturity Amount
                </span>
                <span className="uppercase" style={{ letterSpacing: "0.06em" }}>
                  Age at Maturity
                </span>
                <span className="uppercase" style={{ letterSpacing: "0.06em" }}>
                  Reinvestment Return
                </span>
                <span />
              </div>
              {policies.map((p) => (
                <div
                  key={p.id}
                  className="grid grid-cols-2 md:grid-cols-[1.1fr_1fr_0.8fr_1fr_32px] gap-3 items-center rounded-lg p-3 md:p-0"
                  style={{ background: "#fff", border: `1px solid ${C.parchmentLine}`, borderRadius: 10 }}
                >
                  <input
                    value={p.name}
                    onChange={(e) => updatePolicy(p.id, "name", e.target.value)}
                    className="w-full outline-none bg-transparent px-2 py-1.5 md:m-2"
                    style={{ fontFamily: "Inter, sans-serif", color: C.textInk, fontSize: "13.5px", fontWeight: 600 }}
                  />
                  <div className="md:my-2">
                    <NumberField
                      compact
                      value={p.amount}
                      onChange={(v) => updatePolicy(p.id, "amount", v)}
                      suffix="₹"
                      step={10000}
                    />
                  </div>
                  <div className="md:my-2">
                    <NumberField
                      compact
                      value={p.maturityAge}
                      onChange={(v) => updatePolicy(p.id, "maturityAge", v)}
                      suffix="yrs"
                      step={1}
                    />
                  </div>
                  <div className="md:my-2">
                    <NumberField
                      compact
                      value={p.reinvestReturn}
                      onChange={(v) => updatePolicy(p.id, "reinvestReturn", v)}
                      suffix="%"
                      step={0.5}
                    />
                  </div>
                  <button
                    onClick={() => removePolicy(p.id)}
                    className="flex items-center justify-center rounded-md"
                    style={{ color: C.rust, width: 28, height: 28, justifySelf: "center" }}
                    aria-label={`Remove ${p.name}`}
                  >
                    <Trash2 size={16} />
                  </button>
                  <div
                    className="col-span-2 md:col-span-5 -mt-2 md:mt-0 md:col-start-3 md:row-start-2"
                    style={{ color: C.textMuted, fontSize: "11px" }}
                  >
                    {p.amount > 0 &&
                      (p.maturityAge <= inputs.currentAge
                        ? "Already matured — compounds from today"
                        : p.maturityAge <= inputs.retirementAge
                        ? "Matures before retirement — reinvested until then"
                        : "Matures during retirement — tops up Bucket 3 that year")}
                  </div>
                </div>
              ))}
            </div>
            <button
              onClick={addPolicy}
              className="flex items-center gap-1.5 text-sm rounded-md px-3 py-2 mt-3"
              style={{ color: C.gold2, border: `1px dashed ${C.brass}`, fontFamily: "Inter, sans-serif" }}
            >
              <Plus size={14} /> Add policy
            </button>
          </div>

          {/* Legacy for kids */}
          <div className="mt-8">
            <SectionLabel n="05" title="Legacy for Your Kids" />
            <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
              <NumberField
                label="Estate You Want to Leave (today's value)"
                value={inputs.estateBequest}
                onChange={set("estateBequest")}
                suffix="₹"
                step={100000}
                hint="Set to 0 if you want the corpus to run down to zero"
              />
              <div className="flex flex-col justify-center rounded-lg p-3" style={{ background: "#fff", border: `1px solid ${C.parchmentLine}` }}>
                <span className="text-xs uppercase" style={{ color: C.textMuted, letterSpacing: "0.06em" }}>
                  Estate Goal at Death (future value)
                </span>
                <span style={{ fontFamily: "IBM Plex Mono, monospace", color: C.textInk, fontSize: "15px", marginTop: "4px" }}>
                  {shortInr(calc.legacyFVatDeath)}
                </span>
              </div>
              <div className="flex flex-col justify-center rounded-lg p-3" style={{ background: "#fff", border: `1px solid ${C.parchmentLine}` }}>
                <span className="text-xs uppercase" style={{ color: C.textMuted, letterSpacing: "0.06em" }}>
                  Added to Corpus Required
                </span>
                <span style={{ fontFamily: "IBM Plex Mono, monospace", color: C.textInk, fontSize: "15px", marginTop: "4px" }}>
                  {shortInr(calc.legacyPVatRetirement)}
                </span>
              </div>
            </div>
          </div>

          {/* Bucket config */}
          <div className="mt-8">
            <SectionLabel n="06" title="Three-Bucket Configuration" />
            <div className="grid grid-cols-2 md:grid-cols-3 gap-4 mb-2">
              <NumberField
                label="Bucket 1 — Liquid, covers"
                value={inputs.bucket1Years}
                onChange={set("bucket1Years")}
                suffix="yrs"
              />
              <NumberField
                label="Bucket 1 Return"
                value={inputs.bucket1Return}
                onChange={set("bucket1Return")}
                suffix="%"
                step={0.5}
              />
              <div className="hidden md:block" />
              <NumberField
                label="Bucket 2 — Balanced, covers"
                value={inputs.bucket2Years}
                onChange={set("bucket2Years")}
                suffix="yrs"
              />
              <NumberField
                label="Bucket 2 Return"
                value={inputs.bucket2Return}
                onChange={set("bucket2Return")}
                suffix="%"
                step={0.5}
              />
              <div className="hidden md:block" />
              <div className="flex flex-col justify-center">
                <span className="text-xs uppercase" style={{ color: C.textMuted, letterSpacing: "0.06em" }}>
                  Bucket 3 — Growth, covers
                </span>
                <span
                  style={{
                    fontFamily: "IBM Plex Mono, monospace",
                    color: C.textInk,
                    fontSize: "15px",
                    marginTop: "4px",
                  }}
                >
                  {calc.b3Years} yrs (remainder + legacy)
                </span>
              </div>
              <NumberField
                label="Bucket 3 Return"
                value={inputs.bucket3Return}
                onChange={set("bucket3Return")}
                suffix="%"
                step={0.5}
              />
            </div>
            <div className="flex items-start gap-2 mt-3" style={{ color: C.textMuted, fontSize: "12px" }}>
              <Info size={14} style={{ marginTop: "2px", flexShrink: 0 }} />
              <span>
                Bucket 1 holds cash/liquid funds for near-term withdrawals, Bucket 2 sits in
                balanced/debt-oriented funds, and Bucket 3 stays in equity for long-term growth —
                it also carries the legacy amount you want left over.
              </span>
            </div>
          </div>
        </div>
      </div>

      {/* Results — total corpus picture */}
      <div className="max-w-5xl mx-auto px-6 md:px-12 mt-8">
        <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
          <StatCard
            icon={Landmark}
            label="Total Corpus Required (Bucket-Based)"
            value={inr(calc.corpus)}
            note={`${shortInr(calc.bucket1Target)} + ${shortInr(calc.bucket2Target)} + ${shortInr(calc.bucket3Target)}`}
            color={C.brass}
          />
          <StatCard
            icon={TrendingUp}
            label="Future Monthly Expense (Yr 1 of retirement)"
            value={inr(calc.futureMonthlyExpenseYear1)}
            note={`Real rate of return: ${(calc.realRate * 100).toFixed(2)}%`}
            color={C.teal}
          />
          <StatCard
            icon={Wallet}
            label="Already Covered (Investments + Policies)"
            value={inr(calc.alreadyCovered)}
            note={`${shortInr(calc.alreadyCovered)} projected at retirement`}
            color={C.slate}
          />
        </div>
        <div
          className="flex items-start gap-2 mt-3 rounded-lg px-4 py-3"
          style={{ background: C.ink2, border: `1px solid ${C.inkLine}` }}
        >
          <Info size={14} style={{ marginTop: "2px", flexShrink: 0, color: C.textMuted }} />
          <span style={{ color: C.textMuted, fontSize: "12px" }}>
            Quick cross-check using one blended post-retirement rate ({inputs.postReturn}%) instead
            of each bucket's own rate: <strong style={{ color: C.parchment }}>{inr(calc.corpusQuickEstimate)}</strong>.
            The bucket-based figure above is what drives the SIP — it's usually higher, since Bucket
            1 and Bucket 2 typically earn less than the blended average, and money sitting in a
            lower-return bucket needs a bigger cushion to cover the same expenses.
          </span>
        </div>
      </div>

      {/* Revised SIP */}
      <div className="max-w-5xl mx-auto px-6 md:px-12 mt-4">
        <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
          <StatCard
            icon={Sparkles}
            label="Additional Corpus Still Needed"
            value={inr(calc.additionalCorpusNeeded)}
            note={
              calc.surplus > 0
                ? `Existing plans cover it, surplus ${shortInr(calc.surplus)}`
                : `${shortInr(calc.additionalCorpusNeeded)} left to fund via SIP`
            }
            color={C.rust}
          />
          <StatCard
            icon={PiggyBank}
            label="Revised Monthly SIP Required"
            value={inr(calc.sip)}
            note={`For ${calc.yearsToRetirement} yrs @ ${inputs.preReturn}% return`}
            color={C.brass}
            highlight
          />
          <div className="rounded-xl p-5 flex flex-col justify-center" style={{ background: C.ink2, border: `1px solid ${C.inkLine}` }}>
            <div style={{ color: C.textMuted, fontSize: "11px", textTransform: "uppercase", letterSpacing: "0.05em" }}>
              Coverage of Total Corpus
            </div>
            <div className="rounded-full overflow-hidden mt-3" style={{ height: 10, background: C.inkLine }}>
              <div
                style={{
                  width: `${Math.min(100, (calc.alreadyCovered / (calc.corpus || 1)) * 100)}%`,
                  height: "100%",
                  background: C.brass,
                }}
              />
            </div>
            <div style={{ color: C.textMuted, fontSize: "12px", marginTop: "8px", fontFamily: "IBM Plex Mono, monospace" }}>
              {Math.min(100, (calc.alreadyCovered / (calc.corpus || 1)) * 100).toFixed(0)}% already funded
            </div>
          </div>
        </div>
      </div>

      {/* Legacy check */}
      {inputs.estateBequest > 0 && (
        <div className="max-w-5xl mx-auto px-6 md:px-12 mt-4">
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <StatCard
              icon={Gift}
              label="Estate Goal at Death (future value)"
              value={inr(calc.legacyFVatDeath)}
              note={`In today's rupees: ${inr(inputs.estateBequest)}`}
              color={C.plum}
            />
            <StatCard
              icon={Gift}
              label="Projected Balance at End of Retirement"
              value={inr(calc.projectedLegacy)}
              note={
                calc.projectedLegacy >= calc.legacyFVatDeath
                  ? `On track — ${shortInr(calc.projectedLegacy - calc.legacyFVatDeath)} above goal`
                  : `Short by ${shortInr(calc.legacyFVatDeath - calc.projectedLegacy)} at current assumptions`
              }
              color={calc.projectedLegacy >= calc.legacyFVatDeath ? C.teal : C.rust}
            />
          </div>
        </div>
      )}

      {/* Bucket jars */}
      <div className="max-w-5xl mx-auto px-6 md:px-12 mt-10">
        <div className="rounded-xl p-6 md:p-8" style={{ background: C.ink2, border: `1px solid ${C.inkLine}` }}>
          <SectionLabel n="07" title="The Three Buckets, at Retirement" dark />
          <div className="flex justify-center gap-10 md:gap-16 flex-wrap">
            <Jar
              label="Bucket 1"
              sub={`Liquid · ${calc.b1Years} yrs`}
              amount={calc.bucket1Target}
              pct={(calc.bucket1Target / bucketTotal) * 100}
              color={C.rust}
              colorLight={C.rustLight}
            />
            <Jar
              label="Bucket 2"
              sub={`Balanced · ${calc.b2Years} yrs`}
              amount={calc.bucket2Target}
              pct={(calc.bucket2Target / bucketTotal) * 100}
              color={C.slate}
              colorLight={C.slateLight}
            />
            <Jar
              label="Bucket 3"
              sub={`Growth · ${calc.b3Years} yrs`}
              amount={calc.bucket3Target}
              pct={(calc.bucket3Target / bucketTotal) * 100}
              color={C.teal}
              colorLight={C.tealLight}
            />
          </div>
        </div>
      </div>

      {/* Charts */}
      <div className="max-w-5xl mx-auto px-6 md:px-12 mt-8 grid grid-cols-1 md:grid-cols-2 gap-4">
        <ChartPanel title="08 — Accumulation" subtitle="Existing investments + policies + new SIP building up">
          <ResponsiveContainer width="100%" height={230}>
            <AreaChart data={calc.accumulation}>
              <defs>
                <linearGradient id="existingGrad" x1="0" y1="0" x2="0" y2="1">
                  <stop offset="0%" stopColor={C.slate} stopOpacity={0.6} />
                  <stop offset="100%" stopColor={C.slate} stopOpacity={0.05} />
                </linearGradient>
                <linearGradient id="sipGrad" x1="0" y1="0" x2="0" y2="1">
                  <stop offset="0%" stopColor={C.brass} stopOpacity={0.6} />
                  <stop offset="100%" stopColor={C.brass} stopOpacity={0.05} />
                </linearGradient>
              </defs>
              <CartesianGrid stroke={C.inkLine} strokeDasharray="3 3" />
              <XAxis dataKey="age" stroke={C.textMuted} fontSize={11} />
              <YAxis stroke={C.textMuted} fontSize={11} tickFormatter={(v) => shortInr(v)} width={60} />
              <Tooltip
                formatter={(v) => inr(v)}
                labelFormatter={(v) => `Age ${v}`}
                contentStyle={{ background: C.ink2, border: `1px solid ${C.inkLine}`, borderRadius: 8 }}
                labelStyle={{ color: C.parchment }}
              />
              <Legend wrapperStyle={{ fontSize: "11px", color: C.textMuted }} />
              <Area
                type="monotone"
                dataKey="existing"
                name="Existing + Policies"
                stackId="1"
                stroke={C.slate}
                fill="url(#existingGrad)"
                strokeWidth={2}
              />
              <Area
                type="monotone"
                dataKey="newSip"
                name="New SIP"
                stackId="1"
                stroke={C.brass}
                fill="url(#sipGrad)"
                strokeWidth={2}
              />
            </AreaChart>
          </ResponsiveContainer>
        </ChartPanel>

        <ChartPanel title="09 — Bucket Balances" subtitle="Simulated draw-down through retirement">
          <ResponsiveContainer width="100%" height={230}>
            <BarChart data={calc.rows}>
              <CartesianGrid stroke={C.inkLine} strokeDasharray="3 3" />
              <XAxis dataKey="age" stroke={C.textMuted} fontSize={11} />
              <YAxis stroke={C.textMuted} fontSize={11} tickFormatter={(v) => shortInr(v)} width={60} />
              <Tooltip
                formatter={(v) => inr(v)}
                labelFormatter={(v) => `Age ${v}`}
                contentStyle={{ background: C.ink2, border: `1px solid ${C.inkLine}`, borderRadius: 8 }}
                labelStyle={{ color: C.parchment }}
              />
              <Legend wrapperStyle={{ fontSize: "11px", color: C.textMuted }} />
              <Bar dataKey="b1" stackId="a" name="Bucket 1" fill={C.rust} />
              <Bar dataKey="b2" stackId="a" name="Bucket 2" fill={C.slate} />
              <Bar dataKey="b3" stackId="a" name="Bucket 3" fill={C.teal} />
            </BarChart>
          </ResponsiveContainer>
        </ChartPanel>
      </div>

      {calc.depleted && (
        <div className="max-w-5xl mx-auto px-6 md:px-12 mt-4">
          <div className="rounded-lg px-4 py-3 text-sm" style={{ background: "#3A2320", border: `1px solid ${C.rust}`, color: C.rustLight }}>
            At the current assumptions, all three buckets are projected to run dry around age{" "}
            {calc.depleted.age} — before life expectancy of {inputs.lifeExpectancy}. Consider a
            higher SIP, later retirement, a smaller legacy goal, or higher bucket returns.
          </div>
        </div>
      )}

      {/* Rebalancing ledger table */}
      <div className="max-w-5xl mx-auto px-6 md:px-12 mt-8 mb-12">
        <div className="rounded-xl p-6 md:p-8" style={{ background: C.parchment, border: `1px solid ${C.parchmentLine}` }}>
          <SectionLabel n="10" title="Year-by-Year Rebalancing" />
          <div className="flex items-center gap-2 mb-4" style={{ color: C.textMuted, fontSize: "12px" }}>
            <ArrowRightLeft size={13} />
            <span>
              Each year: any policy maturing that year lands in Bucket 3, then withdraw from
              Bucket 1, grow all three buckets, then top up Bucket 1 from Bucket 2 and Bucket 2
              from Bucket 3.
            </span>
          </div>
          <div className="overflow-x-auto" style={{ maxHeight: "420px", overflowY: "auto" }}>
            <table className="w-full text-sm" style={{ borderCollapse: "collapse", fontFamily: "IBM Plex Mono, monospace" }}>
              <thead>
                <tr style={{ position: "sticky", top: 0, background: C.parchment2 }}>
                  {["Year", "Age", "Expense", "Policy In", "Bucket 1", "Bucket 2", "Bucket 3", "Total", "B2→B1", "B3→B2"].map(
                    (h) => (
                      <th
                        key={h}
                        className="text-right px-3 py-2"
                        style={{
                          color: C.textInk,
                          fontFamily: "Inter, sans-serif",
                          fontSize: "11px",
                          textTransform: "uppercase",
                          letterSpacing: "0.04em",
                          borderBottom: `2px solid ${C.parchmentLine}`,
                        }}
                      >
                        {h}
                      </th>
                    )
                  )}
                </tr>
              </thead>
              <tbody>
                {calc.rows.map((r, i) => (
                  <tr
                    key={r.year}
                    style={{ background: i % 2 === 0 ? "#fff" : C.parchment2, borderBottom: `1px solid ${C.parchmentLine}` }}
                  >
                    <td className="px-3 py-1.5 text-right" style={{ color: C.textMuted }}>{r.year}</td>
                    <td className="px-3 py-1.5 text-right" style={{ color: C.textMuted }}>{r.age}</td>
                    <td className="px-3 py-1.5 text-right" style={{ color: C.textInk }}>{shortInr(r.expense)}</td>
                    <td className="px-3 py-1.5 text-right" style={{ color: C.gold2 }}>
                      {r.licInjection > 0 ? shortInr(r.licInjection) : "—"}
                    </td>
                    <td className="px-3 py-1.5 text-right" style={{ color: C.rust }}>{shortInr(r.b1)}</td>
                    <td className="px-3 py-1.5 text-right" style={{ color: C.slate }}>{shortInr(r.b2)}</td>
                    <td className="px-3 py-1.5 text-right" style={{ color: C.teal }}>{shortInr(r.b3)}</td>
                    <td className="px-3 py-1.5 text-right" style={{ color: C.textInk, fontWeight: 600 }}>{shortInr(r.total)}</td>
                    <td className="px-3 py-1.5 text-right" style={{ color: C.textMuted }}>
                      {r.transferB2toB1 > 1 ? shortInr(r.transferB2toB1) : "—"}
                    </td>
                    <td className="px-3 py-1.5 text-right" style={{ color: C.textMuted }}>
                      {r.transferB3toB2 > 1 ? shortInr(r.transferB3toB2) : "—"}
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <div className="text-center pb-8" style={{ color: C.textMuted, fontSize: "11px" }}>
        Illustrative planning tool — not investment advice. Actual returns and inflation will vary.
      </div>
    </div>
  );
}

function InvestmentTable({ rows, onUpdate, onRemove, columns }) {
  return (
    <div className="flex flex-col gap-3 mb-3">
      <div
        className="hidden md:grid gap-3 px-1"
        style={{ gridTemplateColumns: "1.3fr 1fr 0.8fr 1fr 32px", color: C.textMuted, fontSize: "11px" }}
      >
        {columns.map((c) => (
          <span key={c} className="uppercase" style={{ letterSpacing: "0.06em" }}>
            {c}
          </span>
        ))}
        <span />
      </div>
      {rows.map((inv) => (
        <div
          key={inv.id}
          className="grid grid-cols-2 md:grid-cols-[1.3fr_1fr_0.8fr_1fr_32px] gap-3 items-center rounded-lg p-3 md:p-0"
          style={{ background: "#fff", border: `1px solid ${C.parchmentLine}`, borderRadius: 10 }}
        >
          <input
            value={inv.name}
            onChange={(e) => onUpdate(inv.id, "name", e.target.value)}
            className="w-full outline-none bg-transparent px-2 py-1.5 md:m-2"
            style={{ fontFamily: "Inter, sans-serif", color: C.textInk, fontSize: "13.5px", fontWeight: 600 }}
          />
          <div className="md:my-2">
            <NumberField compact value={inv.amount} onChange={(v) => onUpdate(inv.id, "amount", v)} suffix="₹" step={5000} />
          </div>
          <div className="md:my-2">
            <NumberField compact value={inv.returnRate} onChange={(v) => onUpdate(inv.id, "returnRate", v)} suffix="%" step={0.25} />
          </div>
          <div className="md:my-2">
            <NumberField compact value={inv.monthly} onChange={(v) => onUpdate(inv.id, "monthly", v)} suffix="₹/mo" step={500} />
          </div>
          <button
            onClick={() => onRemove(inv.id)}
            className="flex items-center justify-center rounded-md"
            style={{ color: C.rust, width: 28, height: 28, justifySelf: "center" }}
            aria-label={`Remove ${inv.name}`}
          >
            <Trash2 size={16} />
          </button>
        </div>
      ))}
    </div>
  );
}

function StatCard({ icon: Icon, label, value, note, color, highlight }) {
  return (
    <div
      className="rounded-xl p-5"
      style={{ background: highlight ? C.ink3 : C.ink2, border: `1px solid ${highlight ? C.brass : C.inkLine}` }}
    >
      <div className="flex items-center gap-2 mb-3">
        <div className="rounded-full p-1.5 flex items-center justify-center" style={{ background: `${color}22` }}>
          <Icon size={15} color={color} />
        </div>
        <span style={{ color: C.textMuted, fontSize: "11px", textTransform: "uppercase", letterSpacing: "0.05em" }}>
          {label}
        </span>
      </div>
      <div style={{ fontFamily: "Fraunces, serif", color: C.parchment, fontSize: "24px", fontWeight: 600 }}>{value}</div>
      <div style={{ color: C.textMuted, fontSize: "12px", marginTop: "4px", fontFamily: "IBM Plex Mono, monospace" }}>{note}</div>
    </div>
  );
}

function ChartPanel({ title, subtitle, children }) {
  return (
    <div className="rounded-xl p-5" style={{ background: C.ink2, border: `1px solid ${C.inkLine}` }}>
      <div style={{ fontFamily: "Fraunces, serif", color: C.brass, fontSize: "12px", letterSpacing: "0.08em" }}>{title}</div>
      <div style={{ color: C.textMuted, fontSize: "12px", marginBottom: "8px" }}>{subtitle}</div>
      {children}
    </div>
  );
}
