<script setup lang="ts">
const population: Ref<number> = useState('pop')

await callOnce(async () => {
  population.value = await $fetch('http://127.0.0.1:8080')
})

// Explicit locale rather than toLocaleString(): the server's default locale and the
// browser's can disagree past 1,000, which would surface as a hydration mismatch.
const numberFormat = new Intl.NumberFormat('en-US')
const displayPopulation = computed(() => numberFormat.format(population.value))

async function addResident() {
  const { data } = await $fetch('http://127.0.0.1:8080/add')

  console.log('JON LOG HERE: ', data)
  population.value += 1
}

function removeResident() {
  population.value = Math.max(0, population.value - 1)
}

const services = [
  { code: 'Horizon', port: ':3000', tech: 'Vue & Nuxt', desc: 'The visible edge. What you actually look at.' },
  { code: 'Bedrock', port: ':8080', tech: 'Rust & Axum', desc: 'Structural, load-bearing, out of sight.' },
  { code: 'Cellar', port: ':5432', tech: 'PostgreSQL & SQLx', desc: 'Dug in, cool and dark. It remembers everything.' }
]
</script>

<template>
  <div class="big-sky">
    <NuxtImg
      src="/big-sky-bg.png"
      alt=""
      aria-hidden="true"
      :width="1536"
      :height="1024"
      format="webp"
      fetchpriority="high"
      preload
      class="big-sky__bg"
    />

    <div class="big-sky__scrim" />

    <!-- Carves an inset shadow into the numerals so they read as routed into the wood. -->
    <svg
      class="big-sky__defs"
      aria-hidden="true"
      focusable="false"
    >
      <defs>
        <filter
          id="big-sky-inner-shadow"
          x="-20%"
          y="-20%"
          width="140%"
          height="140%"
        >
          <!-- Invert the alpha to get the "outside" of the glyphs -->
          <feComponentTransfer
            in="SourceAlpha"
            result="invertedAlpha"
          >
            <feFuncA
              type="table"
              tableValues="1 0"
            />
          </feComponentTransfer>

          <!-- Blur and offset it so it bleeds inward from the top -->
          <feGaussianBlur
            in="invertedAlpha"
            stdDeviation="2.5"
            result="blurredInvert"
          />
          <feOffset
            in="blurredInvert"
            dx="1.25"
            dy="2.5"
            result="offsetInvert"
          />

          <!-- Clip the blur back inside the original glyphs -->
          <feComposite
            in="offsetInvert"
            in2="SourceAlpha"
            operator="in"
            result="innerMask"
          />

          <feFlood
            flood-color="rgba(0, 0, 0, 0.75)"
            result="shadowColor"
          />
          <feComposite
            in="shadowColor"
            in2="innerMask"
            operator="in"
            result="innerShadow"
          />

          <feMerge>
            <feMergeNode in="SourceGraphic" />
            <feMergeNode in="innerShadow" />
          </feMerge>
        </filter>
      </defs>
    </svg>

    <!-- 2-up above 64rem: copy on the left, sign on the right. Stacks below. -->
    <div class="big-sky__layout">
      <section class="big-sky__panel">
        <h1 class="big-sky__title">
          Howdy, Stranger 🤠
        </h1>

        <p class="big-sky__lede">
          You just scaffolded a project with the best web stack in the west. Every click on this counter calls your Axum server, which reads and writes to PostgreSQL — your full stack is already live and wired together.
        </p>

        <ul class="big-sky__services">
          <li
            v-for="service in services"
            :key="service.code"
            class="big-sky__service"
          >
            <div class="big-sky__service-id">
              <span class="big-sky__service-code">{{ service.code }}</span>
              <span class="big-sky__service-port">{{ service.port }}</span>
            </div>

            <span
              class="big-sky__service-rule"
              aria-hidden="true"
            />

            <div class="big-sky__service-body">
              <span class="big-sky__service-tech">{{ service.tech }}</span>
              <span class="big-sky__service-desc">{{ service.desc }}</span>
            </div>
          </li>
        </ul>

        <p class="big-sky__signoff">
          Now let's go build something great together.
        </p>
      </section>

      <div class="big-sky__showcase">
        <p class="big-sky__live">
          <span
            class="big-sky__live-dot"
            aria-hidden="true"
          >
            <span class="big-sky__live-ping" />
            <span class="big-sky__live-core" />
          </span>

          <span class="big-sky__live-text">
            Live — routed through <span class="big-sky__live-em">Bedrock</span> &amp; <span class="big-sky__live-em">Cellar</span>
          </span>
        </p>

        <div class="big-sky__sign">
          <NuxtImg
            src="/big-sky-sign.png"
            alt="Big Sky — Population, established 2016"
            :width="1536"
            :height="1024"
            format="webp"
            fetchpriority="high"
            preload
            draggable="false"
            class="big-sky__sign-img"
          />

          <p
            class="big-sky__count"
            aria-live="polite"
          >
            {{ displayPopulation }}
          </p>
        </div>

        <div class="big-sky__actions">
          <button
            type="button"
            class="big-sky__btn"
            :disabled="population === 0"
            @click="removeResident"
          >
            Subtract a person 😵
          </button>

          <button
            type="button"
            class="big-sky__btn"
            @click="addResident"
          >
            Add a person 👶
          </button>
        </div>

        <p class="big-sky__hint">
          Each click calls the API and updates the database ↑
        </p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.big-sky {
  --sign-ink: #e8d098;
  --btn-bg: rgb(220 185 130 / 0.28);
  --btn-bg-hover: rgb(220 185 130 / 0.45);
  --btn-fg: rgb(255 238 200 / 0.95);
  --btn-border: rgb(220 185 130 / 0.45);
  --btn-border-hover: rgb(220 185 130 / 0.7);
  --live: #4ade80;
  --ease-out: cubic-bezier(0, 0, 0.58, 1);

  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  min-height: 100vh;
  min-height: 100dvh;
  padding: 4rem 1.5rem;
  font-family: 'Inter', sans-serif;
}

.big-sky__bg {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center 60%;
}

.big-sky__scrim {
  position: absolute;
  inset: 0;
  background: rgb(0 0 0 / 0.25);
}

/* Filter definitions only — never painted. */
.big-sky__defs {
  position: absolute;
  width: 0;
  height: 0;
}

.big-sky__layout {
  position: relative;
  z-index: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2.5rem;
  width: 100%;

  /* Stacked, this caps the sign and keeps the panel's line length readable.
     The row layout below widens it to the full 64rem. */
  max-width: 36rem;
  padding: 0 1rem;
}

/* ---- Left: copy panel ---- */

.big-sky__panel {
  width: 100%;
  padding: 2rem;
  background: rgb(0 0 0 / 0.58);
  border: 1px solid rgb(255 255 255 / 0.08);
  border-radius: 1rem;
  backdrop-filter: blur(20px);
  animation: big-sky-slide-in 0.5s var(--ease-out) both;
}

.big-sky__title {
  margin: 0 0 0.25rem;
  font-family: 'Sancreek', serif;
  font-size: 2.25rem;
  font-weight: 400;
  line-height: 1.1;
  color: rgb(255 255 255 / 0.95);
}

.big-sky__lede {
  margin: 0.75rem 0 1.5rem;
  font-size: 0.875rem;
  line-height: 1.7;
  color: rgb(255 255 255 / 0.45);
}

.big-sky__services {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  padding: 0;
  margin: 0 0 1.75rem;
  list-style: none;
}

.big-sky__service {
  display: flex;
  align-items: flex-start;
  gap: 0.75rem;
}

.big-sky__service-id {
  display: flex;
  flex-direction: column;
  flex-shrink: 0;
  width: 5rem;
  text-align: right;
}

.big-sky__service-code {
  font-size: 0.875rem;
  font-weight: 600;
  line-height: 1.25rem;
  color: rgb(255 255 255 / 0.85);
}

.big-sky__service-port {
  font-family: ui-monospace, monospace;
  font-size: 0.75rem;
  line-height: 1rem;
  color: rgb(255 255 255 / 0.2);
}

/* Hairline divider between the service name and its description. */
.big-sky__service-rule {
  align-self: stretch;
  width: 1px;
  margin-top: 0.25rem;
  background: rgb(255 255 255 / 0.1);
}

.big-sky__service-body {
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
}

.big-sky__service-tech {
  font-size: 0.75rem;
  line-height: 1rem;
  color: rgb(255 255 255 / 0.35);
}

.big-sky__service-desc {
  font-size: 0.75rem;
  line-height: 1.625;
  color: rgb(255 255 255 / 0.55);
}

.big-sky__signoff {
  margin: 0;
  font-size: 0.875rem;
  font-style: italic;
  line-height: 1.25rem;
  color: rgb(255 255 255 / 0.6);
}

/* ---- Right: sign, buttons, status ---- */

.big-sky__showcase {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}

.big-sky__live {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.375rem 0.75rem;
  margin: 0 0 0.75rem;
  background: rgb(0 0 0 / 0.4);
  border: 1px solid rgb(255 255 255 / 0.08);
  border-radius: 9999px;
}

.big-sky__live-dot {
  position: relative;
  display: flex;
  width: 0.5rem;
  height: 0.5rem;
}

.big-sky__live-ping {
  position: absolute;
  width: 100%;
  height: 100%;
  background: var(--live);
  border-radius: 9999px;
  opacity: 0.75;
  animation: big-sky-ping 1s cubic-bezier(0, 0, 0.2, 1) infinite;
}

.big-sky__live-core {
  position: relative;
  width: 0.5rem;
  height: 0.5rem;
  background: var(--live);
  border-radius: 9999px;
}

.big-sky__live-text {
  font-size: 0.75rem;
  line-height: 1rem;
  color: rgb(255 255 255 / 0.45);
}

.big-sky__live-em {
  color: rgb(255 255 255 / 0.7);
}

.big-sky__sign {
  position: relative;
  width: 100%;
  filter: drop-shadow(0 20px 60px rgb(0 0 0 / 0.6));
  animation: big-sky-rise 0.55s var(--ease-out) both;
}

.big-sky__sign-img {
  display: block;
  width: 100%;
  height: auto;
  user-select: none;
  pointer-events: none;
}

/* Seats the count on the blank plank, between "Population" and "ESTABLISHED 2016". */
.big-sky__count {
  position: absolute;
  top: 52%;
  right: 10%;
  bottom: 24%;
  left: 10%;
  display: grid;
  place-items: center;
  margin: 0;
  font-family: 'Sancreek', serif;
  font-size: clamp(3rem, 10vw, 5rem);
  line-height: 1;
  color: var(--sign-ink);
  text-align: center;
  filter: url(#big-sky-inner-shadow);
}

/* The 9% inset lines the buttons up with the sign's posts. */
.big-sky__actions {
  display: flex;
  gap: 0.75rem;
  width: 100%;
  padding: 0 9%;
  margin-top: 0.25rem;
  animation: big-sky-rise-sm 0.4s var(--ease-out) 0.35s both;
}

.big-sky__btn {
  flex: 1;
  padding: 0.75rem 1rem;
  font-family: inherit;
  font-size: 0.875rem;
  font-weight: 600;
  line-height: 1.25rem;
  color: var(--btn-fg);
  background: var(--btn-bg);
  border: 1px solid var(--btn-border);
  border-radius: 0.75rem;
  box-shadow: 0 2px 16px rgb(0 0 0 / 0.35);
  backdrop-filter: blur(12px);
  cursor: pointer;
  transition: background 0.15s ease, border-color 0.15s ease, transform 120ms ease;
}

.big-sky__btn:hover:not(:disabled) {
  background: var(--btn-bg-hover);
  border-color: var(--btn-border-hover);
}

.big-sky__btn:active:not(:disabled) {
  transform: scale(0.97);
}

.big-sky__btn:disabled {
  opacity: 0.45;
  cursor: not-allowed;
}

.big-sky__hint {
  margin: 0.5rem 0 0;
  font-size: 0.75rem;
  line-height: 1rem;
  color: rgb(255 255 255 / 0.55);
}

@keyframes big-sky-slide-in {
  from {
    opacity: 0;
    transform: translateX(-16px);
  }

  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes big-sky-rise {
  from {
    opacity: 0;
    transform: translateY(16px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes big-sky-rise-sm {
  from {
    opacity: 0;
    transform: translateY(8px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes big-sky-ping {
  75%,
  100% {
    opacity: 0;
    transform: scale(2);
  }
}

@media (min-width: 1024px) {
  .big-sky__layout {
    flex-direction: row;
    max-width: 64rem;
  }

  .big-sky__panel {
    flex: 1;
  }

  .big-sky__showcase {
    flex-shrink: 0;
    width: 45%;
  }
}

/* Both labels wrap to multiple lines side by side below ~480px; stack them. */
@media (max-width: 30rem) {
  .big-sky__actions {
    flex-direction: column;
  }
}

@media (prefers-reduced-motion: reduce) {
  .big-sky__panel,
  .big-sky__sign,
  .big-sky__actions {
    animation: none;
  }

  .big-sky__live-ping {
    animation: none;
  }

  .big-sky__btn {
    transition: none;
  }

  .big-sky__btn:active:not(:disabled) {
    transform: none;
  }
}
</style>
