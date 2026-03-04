<script lang="ts">
  import { onMount, tick } from "svelte";

  let displayValue = "";
  let showModal = false;
  let isThinking = false;
  let showHint = false;
  let showCaptcha = false;
  let captchaNum1 = 0;
  let captchaNum2 = 0;
  let captchaAnswer = "";
  let captchaError = false;

  function handleNumber(num: string) {
    if (isThinking) return;
    displayValue += num;
  }

  function handlePlus() {
    if (isThinking) return;
    displayValue += "+";
  }

  function handlePremiumFeature() {
    if (isThinking) return;
    showModal = true;
  }

  function handleEquals() {
    if (isThinking || displayValue === "") return;

    isThinking = true;
    displayValue = "Thinking...";
    showHint = false;

    setTimeout(() => {
      showHint = true;

      setTimeout(() => {
        isThinking = false;
        showHint = false;
        displayValue = "Hello world";
      }, 5000);
    }, 5000);
  }

  function closeModal() {
    showModal = false;
    showCaptcha = false;
  }

  function handleSubscribe() {
    captchaNum1 = Math.floor(Math.random() * 10) + 1;
    captchaNum2 = Math.floor(Math.random() * 10) + 1;
    captchaAnswer = "";
    captchaError = false;
    showModal = false;
    showCaptcha = true;
  }

  function verifyCaptcha() {
    const sum = captchaNum1 + captchaNum2;
    if (parseInt(captchaAnswer) === sum) {
      window.location.href = "https://youtu.be/4j5EB2doRys?si=0wxyXaPvn-kNZM1c";
    } else {
      captchaError = true;
      captchaAnswer = "";
    }
  }
</script>

<main class="calculator">
  <div class="header">
    <div class="brand">Calc<span class="pro">Pro</span></div>
    <button
      class="theme-toggle"
      on:click={handlePremiumFeature}
      title="Toggle Dark Theme"
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        width="20"
        height="20"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
        ><path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path></svg
      >
    </button>
  </div>

  <div class="screen-container">
    <input
      type="text"
      class="screen {isThinking ? 'thinking' : ''}"
      value={displayValue}
      readonly
      placeholder="0"
    />
    {#if showHint}
      <div class="hint">To think faster, buy a subscription!</div>
    {/if}
  </div>

  <div class="keypad">
    <button class="btn premium" on:click={handlePremiumFeature}>C</button>
    <button class="btn premium" on:click={handlePremiumFeature}>( )</button>
    <button class="btn premium" on:click={handlePremiumFeature}>%</button>
    <button class="btn operator premium" on:click={handlePremiumFeature}
      >/</button
    >

    <button class="btn num" on:click={() => handleNumber("7")}>7</button>
    <button class="btn num" on:click={() => handleNumber("8")}>8</button>
    <button class="btn num" on:click={() => handleNumber("9")}>9</button>
    <button class="btn operator premium" on:click={handlePremiumFeature}
      >*</button
    >

    <button class="btn num" on:click={() => handleNumber("4")}>4</button>
    <button class="btn num" on:click={() => handleNumber("5")}>5</button>
    <button class="btn num" on:click={() => handleNumber("6")}>6</button>
    <button class="btn operator premium" on:click={handlePremiumFeature}
      >-</button
    >

    <button class="btn num" on:click={() => handleNumber("1")}>1</button>
    <button class="btn num" on:click={() => handleNumber("2")}>2</button>
    <button class="btn num" on:click={() => handleNumber("3")}>3</button>
    <button class="btn operator regular" on:click={handlePlus}>+</button>

    <button class="btn num zero" on:click={() => handleNumber("0")}>0</button>
    <button class="btn premium" on:click={handlePremiumFeature}>.</button>
    <button class="btn equals" on:click={handleEquals}>=</button>
  </div>
</main>

{#if showModal}
  <div class="modal-backdrop" on:click={closeModal}>
    <div class="modal" on:click|stopPropagation>
      <h2>Unlock Premium Features</h2>
      <p>
        Upgrade to access subtraction, multiplication, division, decimal points,
        dark mode, and faster thinking!
      </p>

      <div class="plans">
        <div class="plan">
          <h3>PRO</h3>
          <div class="price">$9.99<span>/mo</span></div>
          <ul>
            <li>Subtraction (-)</li>
            <li>Multiplication (*)</li>
            <li>Dark Theme</li>
          </ul>
          <button class="subscribe-btn" on:click={handleSubscribe}
            >Subscribe to PRO</button
          >
        </div>

        <div class="plan ultra">
          <h3>ULTRA</h3>
          <div class="price">$29.99<span>/mo</span></div>
          <ul>
            <li>All PRO features</li>
            <li>Division (/) & Decimals (.)</li>
            <li>Instant Equals (No thinking!)</li>
            <li>Priority Support</li>
          </ul>
          <button class="subscribe-btn ultra-btn" on:click={handleSubscribe}
            >Subscribe to ULTRA</button
          >
        </div>
      </div>

      <button class="close-btn" on:click={closeModal}>Maybe later</button>
    </div>
  </div>
{/if}

{#if showCaptcha}
  <div class="modal-backdrop" on:click={closeModal}>
    <div class="modal captcha-modal" on:click|stopPropagation>
      <h2>Security Check</h2>
      <p>
        Please solve this complex mathematical problem to proceed to payment.
      </p>

      <div class="captcha-box">
        <span class="math-problem">{captchaNum1} + {captchaNum2} = </span>
        <input
          type="number"
          bind:value={captchaAnswer}
          class="captcha-input {captchaError ? 'error' : ''}"
          placeholder="?"
          on:keydown={(e) => e.key === "Enter" && verifyCaptcha()}
          autofocus
        />
      </div>

      {#if captchaError}
        <div class="error-msg">Incorrect. Your math is too weak for Pro!</div>
      {/if}

      <div class="captcha-actions">
        <button class="close-btn" on:click={closeModal}>Cancel</button>
        <button class="verify-btn" on:click={verifyCaptcha}>Verify & Pay</button
        >
      </div>
    </div>
  </div>
{/if}

<style>
  .calculator {
    background: rgba(255, 255, 255, 0.85);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    border-radius: 30px;
    padding: 24px;
    box-shadow:
      0 20px 40px rgba(0, 0, 0, 0.08),
      0 1px 3px rgba(0, 0, 0, 0.05),
      inset 0 0 0 1px rgba(255, 255, 255, 0.5);
    width: 100%;
    max-width: 380px;
    margin: 0 auto;
  }

  .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 24px;
    padding: 0 8px;
  }

  .brand {
    font-size: 1.25rem;
    font-weight: 700;
    color: #1f2937;
    letter-spacing: -0.5px;
  }

  .brand .pro {
    background: linear-gradient(135deg, #6366f1, #a855f7);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    font-weight: 800;
  }

  .theme-toggle {
    background: none;
    border: none;
    color: #6b7280;
    cursor: pointer;
    padding: 8px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.2s ease;
  }

  .theme-toggle:hover {
    background-color: rgba(0, 0, 0, 0.05);
    color: #1f2937;
  }

  .screen-container {
    margin-bottom: 24px;
    position: relative;
  }

  .screen {
    width: 100%;
    background: #ffffff;
    border: 1px solid rgba(0, 0, 0, 0.05);
    border-radius: 20px;
    padding: 24px;
    font-size: 3rem;
    text-align: right;
    color: #111827;
    font-family: inherit;
    font-weight: 300;
    outline: none;
    box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.02);
    transition: all 0.3s ease;
  }

  .screen::placeholder {
    color: #d1d5db;
  }

  .screen.thinking {
    font-size: 2rem;
    color: #6b7280;
    animation: pulse 1.5s infinite;
  }

  @keyframes pulse {
    0% {
      opacity: 0.6;
    }
    50% {
      opacity: 1;
    }
    100% {
      opacity: 0.6;
    }
  }

  .hint {
    position: absolute;
    bottom: -32px;
    left: 0;
    right: 0;
    text-align: center;
    font-size: 0.85rem;
    color: #ef4444;
    font-weight: 600;
    animation: fadeIn 0.3s ease;
    background: rgba(254, 226, 226, 0.95);
    padding: 6px;
    border-radius: 8px;
    backdrop-filter: blur(4px);
    z-index: 10;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }

  .keypad {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 12px;
  }

  .btn {
    appearance: none;
    border: none;
    background: #ffffff;
    border-radius: 16px;
    height: 64px;
    font-size: 1.5rem;
    font-weight: 500;
    color: #374151;
    cursor: pointer;
    box-shadow:
      0 4px 6px rgba(0, 0, 0, 0.02),
      0 1px 3px rgba(0, 0, 0, 0.05),
      inset 0 -2px 0 rgba(0, 0, 0, 0.02);
    transition: all 0.15s cubic-bezier(0.4, 0, 0.2, 1);
    display: flex;
    align-items: center;
    justify-content: center;
    user-select: none;
  }

  .btn.zero {
    grid-column: span 2;
  }

  .btn:hover {
    transform: translateY(-2px);
    box-shadow:
      0 6px 12px rgba(0, 0, 0, 0.05),
      0 2px 4px rgba(0, 0, 0, 0.03),
      inset 0 -2px 0 rgba(0, 0, 0, 0.02);
  }

  .btn:active {
    transform: translateY(1px);
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
  }

  .btn.operator {
    background: #f3f4f6;
    color: #6366f1;
    font-size: 1.75rem;
  }

  .btn.operator.regular {
    background: #f3f4f6;
    color: #10b981;
  }

  .btn.equals {
    background: linear-gradient(135deg, #10b981, #059669);
    color: white;
    box-shadow:
      0 4px 10px rgba(16, 185, 129, 0.3),
      inset 0 -2px 0 rgba(0, 0, 0, 0.1);
  }

  .btn.premium {
    position: relative;
    overflow: hidden;
  }

  .btn.premium::after {
    content: "";
    position: absolute;
    top: 6px;
    right: 6px;
    width: 6px;
    height: 6px;
    background-color: #f59e0b;
    border-radius: 50%;
  }

  /* Modal Styles */
  .modal-backdrop {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: rgba(0, 0, 0, 0.3);
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 100;
    animation: fadeIn 0.2s ease-out;
    padding: 16px;
    box-sizing: border-box;
  }

  .modal {
    background: white;
    border-radius: 24px;
    padding: 32px;
    width: 100%;
    max-width: 600px;
    box-shadow:
      0 25px 50px -12px rgba(0, 0, 0, 0.25),
      0 0 0 1px rgba(0, 0, 0, 0.05);
    text-align: center;
    animation: slideUp 0.3s cubic-bezier(0.16, 1, 0.3, 1);
  }

  .modal h2 {
    margin: 0 0 8px 0;
    font-size: 1.75rem;
    color: #111827;
  }

  .modal > p {
    color: #6b7280;
    margin-bottom: 32px;
    font-size: 1rem;
    line-height: 1.5;
  }

  .plans {
    display: flex;
    gap: 16px;
    margin-bottom: 24px;
  }

  @media (max-width: 640px) {
    .plans {
      flex-direction: column;
    }
  }

  .plan {
    flex: 1;
    border: 2px solid #e5e7eb;
    border-radius: 20px;
    padding: 24px;
    text-align: left;
    transition: all 0.2s ease;
  }

  .plan:hover {
    border-color: #6366f1;
    transform: translateY(-4px);
    box-shadow: 0 12px 24px -8px rgba(99, 102, 241, 0.2);
  }

  .plan.ultra {
    background: linear-gradient(to bottom right, #faf5ff, #f3e8ff);
    border-color: #d8b4fe;
    position: relative;
  }

  .plan.ultra::before {
    content: "MOST POPULAR";
    position: absolute;
    top: -12px;
    left: 50%;
    transform: translateX(-50%);
    background: linear-gradient(135deg, #a855f7, #6366f1);
    color: white;
    font-size: 0.7rem;
    font-weight: 700;
    padding: 4px 12px;
    border-radius: 12px;
    letter-spacing: 0.5px;
  }

  .plan.ultra:hover {
    border-color: #a855f7;
    box-shadow: 0 12px 24px -8px rgba(168, 85, 247, 0.2);
  }

  .plan h3 {
    margin: 0 0 12px 0;
    font-size: 1.1rem;
    color: #374151;
  }

  .plan.ultra h3 {
    color: #7e22ce;
  }

  .price {
    font-size: 2.5rem;
    font-weight: 800;
    color: #111827;
    margin-bottom: 16px;
    display: flex;
    align-items: baseline;
  }

  .price span {
    font-size: 1rem;
    font-weight: 500;
    color: #6b7280;
    margin-left: 4px;
  }

  .plan ul {
    list-style: none;
    padding: 0;
    margin: 0 0 24px 0;
  }

  .plan li {
    font-size: 0.95rem;
    color: #4b5563;
    padding: 8px 0;
    border-bottom: 1px solid #f3f4f6;
    display: flex;
    align-items: center;
  }

  .plan li::before {
    content: "✓";
    color: #10b981;
    margin-right: 8px;
    font-weight: bold;
  }

  .plan.ultra li {
    border-bottom-color: rgba(168, 85, 247, 0.1);
  }

  .subscribe-btn {
    width: 100%;
    padding: 12px;
    border-radius: 12px;
    border: none;
    background: #f3f4f6;
    color: #374151;
    font-weight: 600;
    font-size: 1rem;
    cursor: pointer;
    transition: all 0.2s ease;
  }

  .plan:hover .subscribe-btn {
    background: #6366f1;
    color: white;
  }

  .ultra-btn {
    background: #a855f7;
    color: white;
  }

  .plan.ultra:hover .ultra-btn {
    background: #9333ea;
  }

  .close-btn {
    background: none;
    border: none;
    color: #9ca3af;
    font-size: 0.9rem;
    font-weight: 500;
    cursor: pointer;
    text-decoration: underline;
    transition: color 0.2s ease;
  }

  .close-btn:hover {
    color: #4b5563;
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }

  @keyframes slideUp {
    from {
      opacity: 0;
      transform: translateY(20px) scale(0.95);
    }
    to {
      opacity: 1;
      transform: translateY(0) scale(1);
    }
  }

  /* Captcha Styles */
  .captcha-modal {
    max-width: 400px;
  }

  .captcha-box {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 16px;
    margin: 24px 0;
    font-size: 2rem;
    font-weight: 700;
    color: #1f2937;
  }

  .captcha-input {
    width: 80px;
    height: 60px;
    font-size: 2rem;
    text-align: center;
    border: 2px solid #e5e7eb;
    border-radius: 12px;
    outline: none;
    transition: all 0.2s;
  }

  .captcha-input:focus {
    border-color: #6366f1;
    box-shadow: 0 0 0 4px rgba(99, 102, 241, 0.1);
  }

  .captcha-input.error {
    border-color: #ef4444;
    animation: shake 0.4s ease-in-out;
  }

  .error-msg {
    color: #ef4444;
    font-size: 0.9rem;
    margin-bottom: 24px;
    font-weight: 500;
  }

  .captcha-actions {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 16px;
  }

  .verify-btn {
    background: #10b981;
    color: white;
    border: none;
    padding: 12px 24px;
    border-radius: 12px;
    font-weight: 600;
    font-size: 1rem;
    cursor: pointer;
    transition: all 0.2s;
  }

  .verify-btn:hover {
    background: #059669;
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(16, 185, 129, 0.2);
  }

  @keyframes shake {
    0%,
    100% {
      transform: translateX(0);
    }
    25% {
      transform: translateX(-8px);
    }
    75% {
      transform: translateX(8px);
    }
  }
</style>
