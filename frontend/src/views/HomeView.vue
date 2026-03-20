<template>
  <!-- Custom Home Content: Full Page Mode -->
  <div v-if="homeContent" class="min-h-screen">
    <!-- iframe mode -->
    <iframe
      v-if="isHomeContentUrl"
      :src="homeContent.trim()"
      class="h-screen w-full border-0"
      allowfullscreen
    ></iframe>
    <!-- HTML mode - SECURITY: homeContent is admin-only setting, XSS risk is acceptable -->
    <div v-else v-html="homeContent"></div>
  </div>

    <!-- Default Home Page -->
    <div
      v-else
      class="relative flex min-h-screen flex-col overflow-hidden bg-black dark:bg-black"
    >
    <!-- Background Decorations -->
    <div class="pointer-events-none absolute inset-0 overflow-hidden">
      <!-- Deep blue radial glow top-left -->
      <div
        class="absolute -left-40 -top-40 h-[500px] w-[500px] rounded-full bg-[radial-gradient(circle,rgba(6,182,212,0.25)_0%,rgba(8,145,178,0.10)_40%,transparent_70%)] blur-3xl"
      ></div>
      <!-- Cyan radial glow top-right -->
      <div
        class="absolute -right-40 -top-20 h-[450px] w-[450px] rounded-full bg-[radial-gradient(circle,rgba(6,182,212,0.20)_0%,rgba(6,182,212,0.05)_40%,transparent_70%)] blur-3xl"
      ></div>
      <!-- Accent glow bottom-center -->
      <div
        class="absolute bottom-0 left-1/2 h-[400px] w-[800px] -translate-x-1/2 rounded-full bg-[radial-gradient(ellipse,rgba(6,182,212,0.12)_0%,transparent_70%)] blur-3xl"
      ></div>
      <!-- Subtle grid overlay -->
      <div
        class="absolute inset-0 bg-[linear-gradient(rgba(6,182,212,0.03)_1px,transparent_1px),linear-gradient(90deg,rgba(6,182,212,0.03)_1px,transparent_1px)] bg-[size:64px_64px]"
      ></div>
      <!-- Top border accent line -->
      <div class="absolute inset-x-0 top-0 h-px bg-gradient-to-r from-transparent via-cyan-500/30 to-transparent"></div>
    </div>

    <!-- Header -->
    <header class="relative z-20 px-6 py-4">
      <nav class="mx-auto flex max-w-6xl items-center justify-between">
        <!-- Logo -->
        <div class="flex items-center">
          <div class="h-10 w-10 overflow-hidden rounded-xl shadow-md">
            <img :src="siteLogo || '/logo.png'" alt="Logo" class="h-full w-full object-contain" />
          </div>
        </div>

        <!-- Nav Actions -->
        <div class="flex items-center gap-3">
          <!-- Language Switcher -->
          <LocaleSwitcher />

          <!-- Doc Link -->
          <a
            v-if="docUrl"
            :href="docUrl"
            target="_blank"
            rel="noopener noreferrer"
            class="rounded-lg p-2 text-white/60 transition-colors hover:bg-white/10 hover:text-white"
            :title="t('home.viewDocs')"
          >
            <Icon name="book" size="md" />
          </a>

          <!-- Theme Toggle -->
          <button
            @click="toggleTheme"
            class="rounded-lg p-2 text-white/60 transition-colors hover:bg-white/10 hover:text-white"
            :title="isDark ? t('home.switchToLight') : t('home.switchToDark')"
          >
            <Icon v-if="isDark" name="sun" size="md" />
            <Icon v-else name="moon" size="md" />
          </button>

          <!-- Login / Dashboard Button -->
          <router-link
            v-if="isAuthenticated"
            :to="dashboardPath"
            class="inline-flex items-center gap-1.5 rounded-full bg-gradient-to-r from-cyan-600 to-cyan-600 py-1 pl-1 pr-2.5 shadow-lg shadow-cyan-500/30 transition-all hover:from-cyan-500 hover:to-cyan-500 hover:shadow-cyan-500/40"
          >
            <span
              class="flex h-5 w-5 items-center justify-center rounded-full bg-white/20 text-[10px] font-semibold text-white backdrop-blur-sm"
            >
              {{ userInitial }}
            </span>
            <span class="text-xs font-medium text-white">{{ t('home.dashboard') }}</span>
            <svg
              class="h-3 w-3 text-white/60"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
              stroke-width="2"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M4.5 19.5l15-15m0 0H8.25m11.25 0v11.25"
              />
            </svg>
          </router-link>
          <router-link
            v-else
            to="/login"
            class="inline-flex items-center rounded-full bg-gradient-to-r from-cyan-600 to-cyan-600 px-4 py-1.5 text-xs font-semibold text-white shadow-lg shadow-cyan-500/30 transition-all hover:from-cyan-500 hover:to-cyan-500 hover:shadow-cyan-500/40 hover:shadow-cyan-500/40"
          >
            {{ t('home.login') }}
          </router-link>
        </div>
      </nav>
    </header>

    <!-- Main Content -->
    <main class="relative z-10 flex-1 px-6 py-16">
      <div class="mx-auto max-w-6xl">
        <!-- Hero Section - Left/Right Layout -->
        <div class="mb-12 flex flex-col items-center justify-between gap-12 lg:flex-row lg:gap-16">
          <!-- Left: Text Content -->
          <div class="flex-1 text-center lg:text-left">
            <h1
              class="mb-4 text-4xl font-bold text-white md:text-5xl lg:text-6xl"
            >
              {{ t('home.heroTitle') }}
            </h1>
            <p class="mb-3 text-lg text-white/70 md:text-xl">
              {{ t('home.heroSubtitle') }}
            </p>
            <p class="mb-8 text-sm text-white/40 md:text-base">
              {{ t('home.heroDescription') }}
            </p>

            <!-- CTA Button -->
            <div class="flex flex-wrap items-center gap-3">
              <router-link
                :to="isAuthenticated ? dashboardPath : '/login'"
                class="btn btn-primary px-8 py-3 text-base shadow-xl shadow-cyan-500/40"
              >
                {{ isAuthenticated ? t('home.goToDashboard') : t('home.getStarted') }}
                <Icon name="arrowRight" size="md" class="ml-2" :stroke-width="2" />
              </router-link>
              <a
                v-if="docUrl"
                :href="docUrl"
                target="_blank"
                rel="noopener noreferrer"
                class="inline-flex items-center gap-2 rounded-full border border-white/20 bg-white/5 px-5 py-3 text-sm font-medium text-white/70 backdrop-blur-sm transition-all hover:border-cyan-500/40 hover:bg-cyan-500/5 hover:text-cyan-300"
              >
                <Icon name="book" size="sm" />
                {{ t('home.viewDocs') }}
              </a>
            </div>
          </div>

          <!-- Right: Terminal Animation -->
          <div class="flex flex-1 justify-center lg:justify-end">
            <div class="terminal-container">
              <div class="terminal-window">
                <!-- Window header -->
                <div class="terminal-header">
                  <div class="terminal-buttons">
                    <span class="btn-close"></span>
                    <span class="btn-minimize"></span>
                    <span class="btn-maximize"></span>
                  </div>
                  <span class="terminal-title">terminal</span>
                </div>
                <!-- Terminal content -->
                <div class="terminal-body">
                  <div class="code-line">
                    <span class="code-prompt">></span> <span class="code-method">POST /v1/messages</span>
                  </div>
                  <div class="code-line">
                    <span class="code-success">200 OK</span>
                    <span class="code-separator">|</span>
                    <span class="code-latency">{{ latency }}ms</span>
                    <span class="code-separator">|</span>
                    <span class="code-tokens">{{ tokens }} tokens</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Feature Tags -->
        <div class="mb-12 flex flex-wrap items-center justify-center gap-4 md:gap-6">
          <div
            class="inline-flex items-center gap-2.5 rounded-full border border-cyan-500/20 bg-cyan-500/10 px-5 py-2.5 backdrop-blur-sm"
          >
            <Icon name="swap" size="sm" class="text-cyan-400" />
            <span class="text-sm font-medium text-white/80">{{
              t('home.tags.losslessProxy')
            }}</span>
          </div>
          <div
            class="inline-flex items-center gap-2.5 rounded-full border border-cyan-500/20 bg-cyan-500/10 px-5 py-2.5 backdrop-blur-sm"
          >
            <Icon name="chart" size="sm" class="text-cyan-400" />
            <span class="text-sm font-medium text-white/80">{{
              t('home.tags.payAsYouGo')
            }}</span>
          </div>
          <div
            class="inline-flex items-center gap-2.5 rounded-full border border-cyan-500/20 bg-cyan-500/10 px-5 py-2.5 backdrop-blur-sm"
          >
            <Icon name="bolt" size="sm" class="text-cyan-400" />
            <span class="text-sm font-medium text-white/80">{{
              t('home.tags.promptCache')
            }}</span>
          </div>
          <div
            class="inline-flex items-center gap-2.5 rounded-full border border-cyan-500/20 bg-cyan-500/10 px-5 py-2.5 backdrop-blur-sm"
          >
            <Icon name="shield" size="sm" class="text-cyan-400" />
            <span class="text-sm font-medium text-white/80">{{
              t('home.tags.zeroLog')
            }}</span>
          </div>
        </div>

        <!-- Features Grid -->
        <div class="mb-12 grid gap-6 md:grid-cols-3">
          <!-- Feature 1: Zero Tampering -->
          <div
            class="feature-card group rounded-2xl border border-cyan-500/15 bg-cyan-500/5 p-6 backdrop-blur-sm transition-all duration-300 hover:border-cyan-500/30 hover:bg-cyan-500/10"
          >
            <div
              class="mb-4 flex h-12 w-12 items-center justify-center rounded-xl bg-gradient-to-br from-cyan-500 to-cyan-600 shadow-lg shadow-cyan-500/20 transition-transform group-hover:scale-110"
            >
              <Icon name="checkCircle" size="lg" class="text-white" />
            </div>
            <h3 class="mb-2 text-lg font-semibold text-white">
              {{ t('home.features.zeroTamper.title') }}
            </h3>
            <p class="mb-3 text-sm leading-relaxed text-white/50">
              {{ t('home.features.zeroTamper.desc') }}
            </p>
            <div class="flex flex-wrap gap-2">
              <span class="inline-block rounded bg-cyan-500/10 px-2 py-0.5 text-xs text-cyan-400/80">{{ t('home.features.zeroTamper.tag1') }}</span>
              <span class="inline-block rounded bg-cyan-500/10 px-2 py-0.5 text-xs text-cyan-400/80">{{ t('home.features.zeroTamper.tag2') }}</span>
              <span class="inline-block rounded bg-cyan-500/10 px-2 py-0.5 text-xs text-cyan-400/80">{{ t('home.features.zeroTamper.tag3') }}</span>
            </div>
          </div>

          <!-- Feature 2: Always Online + Prompt Cache -->
          <div
            class="feature-card group rounded-2xl border border-cyan-500/15 bg-cyan-500/5 p-6 backdrop-blur-sm transition-all duration-300 hover:border-cyan-500/30 hover:bg-cyan-500/10"
          >
            <div
              class="mb-4 flex h-12 w-12 items-center justify-center rounded-xl bg-gradient-to-br from-green-500 to-emerald-600 shadow-lg shadow-green-500/20 transition-transform group-hover:scale-110"
            >
              <Icon name="server" size="lg" class="text-white" />
            </div>
            <h3 class="mb-2 text-lg font-semibold text-white">
              {{ t('home.features.alwaysOnline.title') }}
            </h3>
            <p class="mb-3 text-sm leading-relaxed text-white/50">
              {{ t('home.features.alwaysOnline.desc') }}
            </p>
            <div class="flex flex-wrap gap-2">
              <span class="inline-block rounded bg-green-500/10 px-2 py-0.5 text-xs text-green-400/80">{{ t('home.features.alwaysOnline.tag1') }}</span>
              <span class="inline-block rounded bg-green-500/10 px-2 py-0.5 text-xs text-green-400/80">{{ t('home.features.alwaysOnline.tag2') }}</span>
              <span class="inline-block rounded bg-green-500/10 px-2 py-0.5 text-xs text-green-400/80">{{ t('home.features.alwaysOnline.tag3') }}</span>
            </div>
          </div>

          <!-- Feature 3: Pay as You Go -->
          <div
            class="feature-card group rounded-2xl border border-cyan-500/15 bg-cyan-500/5 p-6 backdrop-blur-sm transition-all duration-300 hover:border-cyan-500/30 hover:bg-cyan-500/10"
          >
            <div
              class="mb-4 flex h-12 w-12 items-center justify-center rounded-xl bg-gradient-to-br from-amber-400 to-orange-500 shadow-lg shadow-amber-500/20 transition-transform group-hover:scale-110"
            >
              <Icon name="dollar" size="lg" class="text-white" />
            </div>
            <h3 class="mb-2 text-lg font-semibold text-white">
              {{ t('home.features.payAsYouGo.title') }}
            </h3>
            <p class="mb-3 text-sm leading-relaxed text-white/50">
              {{ t('home.features.payAsYouGo.desc') }}
            </p>
            <div class="flex flex-wrap gap-2">
              <span class="inline-block rounded bg-amber-500/10 px-2 py-0.5 text-xs text-amber-400/80">{{ t('home.features.payAsYouGo.tag1') }}</span>
              <span class="inline-block rounded bg-amber-500/10 px-2 py-0.5 text-xs text-amber-400/80">{{ t('home.features.payAsYouGo.tag2') }}</span>
              <span class="inline-block rounded bg-amber-500/10 px-2 py-0.5 text-xs text-amber-400/80">{{ t('home.features.payAsYouGo.tag3') }}</span>
            </div>
          </div>
        </div>

        <!-- Supported Providers -->
        <div class="mb-8 text-center">
          <h2 class="mb-3 text-2xl font-bold text-white">
            {{ t('home.providers.title') }}
          </h2>
          <p class="text-sm text-white/50">
            {{ t('home.providers.description') }}
          </p>
        </div>

        <div class="mb-16 flex flex-wrap items-center justify-center gap-4">
          <!-- Claude - Supported -->
          <div
            class="provider-card flex items-center gap-2 rounded-xl border border-cyan-500/20 bg-cyan-500/5 px-5 py-3 backdrop-blur-sm"
          >
            <div
              class="flex h-8 w-8 items-center justify-center rounded-lg bg-gradient-to-br from-orange-400 to-orange-500"
            >
              <span class="text-xs font-bold text-white">C</span>
            </div>
            <span class="text-sm font-medium text-white/80">{{ t('home.providers.claude') }}</span>
            <span
              class="rounded bg-cyan-500/20 px-1.5 py-0.5 text-[10px] font-medium text-cyan-300"
              >{{ t('home.providers.supported') }}</span
            >
          </div>
          <!-- GPT - Supported -->
          <div
            class="provider-card flex items-center gap-2 rounded-xl border border-cyan-500/20 bg-cyan-500/5 px-5 py-3 backdrop-blur-sm"
          >
            <div
              class="flex h-8 w-8 items-center justify-center rounded-lg bg-gradient-to-br from-green-500 to-green-600"
            >
              <span class="text-xs font-bold text-white">G</span>
            </div>
            <span class="text-sm font-medium text-white/80">GPT</span>
            <span
              class="rounded bg-cyan-500/20 px-1.5 py-0.5 text-[10px] font-medium text-cyan-300"
              >{{ t('home.providers.supported') }}</span
            >
          </div>
          <!-- Gemini - Supported -->
          <div
            class="provider-card flex items-center gap-2 rounded-xl border border-cyan-500/20 bg-cyan-500/5 px-5 py-3 backdrop-blur-sm"
          >
            <div
              class="flex h-8 w-8 items-center justify-center rounded-lg bg-gradient-to-br from-cyan-500 to-cyan-600"
            >
              <span class="text-xs font-bold text-white">G</span>
            </div>
            <span class="text-sm font-medium text-white/80">{{ t('home.providers.gemini') }}</span>
            <span
              class="rounded bg-cyan-500/20 px-1.5 py-0.5 text-[10px] font-medium text-cyan-300"
              >{{ t('home.providers.supported') }}</span
            >
          </div>
          <!-- More - Coming Soon -->
          <div
            class="flex items-center gap-2 rounded-xl border border-white/10 bg-white/5 px-5 py-3 opacity-50 backdrop-blur-sm"
          >
            <div
              class="flex h-8 w-8 items-center justify-center rounded-lg bg-gradient-to-br from-gray-500 to-gray-600"
            >
              <span class="text-xs font-bold text-white">+</span>
            </div>
            <span class="text-sm font-medium text-white/60">{{ t('home.providers.more') }}</span>
            <span
              class="rounded bg-white/10 px-1.5 py-0.5 text-[10px] font-medium text-white/40"
              >{{ t('home.providers.soon') }}</span
            >
          </div>
        </div>

        <!-- Availability Stats Bar -->
        <div class="stats-container mb-20 rounded-2xl border border-cyan-500/15 bg-cyan-500/5 p-6 backdrop-blur-sm">
          <div class="mb-5 flex w-full flex-wrap items-center justify-around gap-8 md:gap-12">
            <div class="stat-item flex flex-col items-center gap-2">
              <div class="flex items-baseline gap-1">
                <span class="stat-number text-3xl font-bold text-cyan-400">99.9%</span>
                <span class="text-sm font-medium text-white/50">API</span>
              </div>
              <div class="stat-bar-container h-1.5 w-32 rounded-full bg-white/10 overflow-hidden">
                <div class="stat-bar stat-bar-cyan h-full w-full rounded-full bg-gradient-to-r from-cyan-500 to-cyan-400" style="width: 99.9%"></div>
              </div>
            </div>
            <div class="stat-item flex flex-col items-center gap-2">
              <div class="flex items-baseline gap-1">
                <span class="stat-number text-3xl font-bold text-green-400">97%</span>
                <span class="text-sm font-medium text-white/50">Cache</span>
              </div>
              <div class="stat-bar-container h-1.5 w-32 rounded-full bg-white/10 overflow-hidden">
                <div class="stat-bar stat-bar-green h-full w-full rounded-full bg-gradient-to-r from-green-500 to-green-400" style="width: 97%"></div>
              </div>
            </div>
            <div class="stat-item flex flex-col items-center gap-2">
              <div class="flex items-baseline gap-1">
                <span class="stat-number text-3xl font-bold text-amber-400">99.95%</span>
                <span class="text-sm font-medium text-white/50">Gateway</span>
              </div>
              <div class="stat-bar-container h-1.5 w-32 rounded-full bg-white/10 overflow-hidden">
                <div class="stat-bar stat-bar-amber h-full w-full rounded-full bg-gradient-to-r from-amber-500 to-amber-400" style="width: 99.95%"></div>
              </div>
            </div>
          </div>
        </div>

        <!-- How It Works -->
        <div class="mb-20 text-center">
          <h2 class="mb-3 text-2xl font-bold text-white md:text-3xl">
            {{ t('home.howItWorks.title') }}
          </h2>
          <p class="mb-10 text-sm text-white/50">
            {{ t('home.howItWorks.subtitle') }}
          </p>
          <div class="grid gap-6 md:grid-cols-3">
            <div class="relative rounded-2xl border border-cyan-500/15 bg-cyan-500/5 p-6 text-center backdrop-blur-sm">
              <div class="absolute -top-4 left-1/2 -translate-x-1/2">
                <div class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan-500 text-sm font-bold text-black">1</div>
              </div>
              <div class="mb-4 mt-2 flex h-12 w-12 items-center justify-center rounded-xl bg-cyan-500/10 mx-auto">
                <svg class="h-6 w-6 text-cyan-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 5.25a3 3 0 013 3m3 0a6 6 0 01-7.029 5.912c-.563-.097-1.159.026-1.563.43L10.5 17.25H8.25v2.25H6v2.25H2.25v-2.818c0-.597.237-1.17.659-1.591l6.499-6.499c.404-.404.527-1 .43-1.563A6 6 0 1121.75 8.25z" /></svg>
              </div>
              <h3 class="mb-2 text-base font-semibold text-white">{{ t('home.howItWorks.step1.title') }}</h3>
              <p class="text-sm text-white/50">{{ t('home.howItWorks.step1.desc') }}</p>
            </div>
            <div class="relative rounded-2xl border border-cyan-500/15 bg-cyan-500/5 p-6 text-center backdrop-blur-sm">
              <div class="absolute -top-4 left-1/2 -translate-x-1/2">
                <div class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan-500 text-sm font-bold text-black">2</div>
              </div>
              <div class="mb-4 mt-2 flex h-12 w-12 items-center justify-center rounded-xl bg-cyan-500/10 mx-auto">
                <svg class="h-6 w-6 text-cyan-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5"><path stroke-linecap="round" stroke-linejoin="round" d="M10.5 6a7.5 7.5 0 107.5 7.5h-7.5V6z" /><path stroke-linecap="round" stroke-linejoin="round" d="M13.5 10.5H21A7.5 7.5 0 0013.5 3v7.5z" /></svg>
              </div>
              <h3 class="mb-2 text-base font-semibold text-white">{{ t('home.howItWorks.step2.title') }}</h3>
              <p class="text-sm text-white/50">{{ t('home.howItWorks.step2.desc') }}</p>
            </div>
            <div class="relative rounded-2xl border border-cyan-500/15 bg-cyan-500/5 p-6 text-center backdrop-blur-sm">
              <div class="absolute -top-4 left-1/2 -translate-x-1/2">
                <div class="flex h-8 w-8 items-center justify-center rounded-full bg-cyan-500 text-sm font-bold text-black">3</div>
              </div>
              <div class="mb-4 mt-2 flex h-12 w-12 items-center justify-center rounded-xl bg-cyan-500/10 mx-auto">
                <svg class="h-6 w-6 text-cyan-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5"><path stroke-linecap="round" stroke-linejoin="round" d="M3.75 13.5l10.5-11.25L12 10.5h8.25L9.75 21.75 12 13.5H3.75z" /></svg>
              </div>
              <h3 class="mb-2 text-base font-semibold text-white">{{ t('home.howItWorks.step3.title') }}</h3>
              <p class="text-sm text-white/50">{{ t('home.howItWorks.step3.desc') }}</p>
            </div>
          </div>
        </div>

        <!-- Comparison Table -->
        <div class="mb-20 text-center">
          <h2 class="mb-3 text-2xl font-bold text-white md:text-3xl">
            {{ t('home.comparison.title') }}
          </h2>
          <p class="mb-10 text-sm text-white/50">
            {{ t('home.comparison.subtitle') }}
          </p>
          <div class="overflow-hidden rounded-2xl border border-cyan-500/20 backdrop-blur-sm">
            <table class="w-full text-sm">
              <thead>
                <tr class="border-b border-cyan-500/20 bg-cyan-500/10">
                  <th class="py-4 pl-6 text-left font-semibold text-white/70">{{ t('home.comparison.col.feature') }}</th>
                  <th class="py-4 text-center font-semibold text-white/50">{{ t('home.comparison.col.official') }}</th>
                  <th class="py-4 pr-6 text-center font-semibold text-cyan-400">{{ t('home.comparison.col.platform') }}</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, key) in comparisonItems" :key="key" class="border-b border-white/5 transition-colors hover:bg-cyan-500/5">
                  <td class="py-4 pl-6 text-left text-white/70">{{ item.feature }}</td>
                  <td class="py-4 text-center text-white/40">
                    <span class="inline-flex items-center gap-1">
                      <svg class="h-4 w-4 text-red-400/60" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2"><path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" /></svg>
                      {{ item.official }}
                    </span>
                  </td>
                  <td class="py-4 pr-6 text-center text-cyan-300">
                    <span class="inline-flex items-center gap-1">
                      <svg class="h-4 w-4 text-cyan-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2"><path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12.75l6 6 9-13.5" /></svg>
                      {{ item.us }}
                    </span>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>

        <!-- Final CTA -->
        <div class="mb-16 rounded-2xl border border-cyan-500/20 bg-gradient-to-br from-cyan-500/10 to-transparent p-8 text-center backdrop-blur-sm md:p-12">
          <h2 class="mb-3 text-2xl font-bold text-white md:text-3xl">
            {{ t('home.finalCta.title') }}
          </h2>
          <p class="mb-6 text-sm text-white/50">
            {{ t('home.finalCta.subtitle') }}
          </p>
          <router-link
            :to="isAuthenticated ? dashboardPath : '/login'"
            class="btn btn-primary px-10 py-3 text-base shadow-xl shadow-cyan-500/40"
          >
            {{ isAuthenticated ? t('home.goToDashboard') : t('home.getStarted') }}
            <Icon name="arrowRight" size="md" class="ml-2" :stroke-width="2" />
          </router-link>
        </div>
      </div>
    </main>

    <!-- Footer -->
    <footer class="relative z-10 border-t border-white/10 px-6 py-8">
      <div
        class="mx-auto flex max-w-6xl flex-col items-center justify-center gap-4 text-center sm:flex-row sm:text-left"
      >
        <p class="text-sm text-white/40">
          &copy; {{ currentYear }} {{ siteName }}. {{ t('home.footer.allRightsReserved') }}
        </p>
        <div class="flex items-center gap-4">
          <a
            v-if="docUrl"
            :href="docUrl"
            target="_blank"
            rel="noopener noreferrer"
            class="text-sm text-white/40 transition-colors hover:text-white/70"
          >
            {{ t('home.docs') }}
          </a>
          <a
            :href="githubUrl"
            target="_blank"
            rel="noopener noreferrer"
            class="text-sm text-white/40 transition-colors hover:text-white/70"
          >
            GitHub
          </a>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import { useI18n } from 'vue-i18n'
import { useAuthStore, useAppStore } from '@/stores'
import LocaleSwitcher from '@/components/common/LocaleSwitcher.vue'
import Icon from '@/components/icons/Icon.vue'

const { t } = useI18n()

const authStore = useAuthStore()
const appStore = useAppStore()

// Site settings - directly from appStore (already initialized from injected config)
const siteName = computed(() => appStore.cachedPublicSettings?.site_name || appStore.siteName || 'Sub2API')
const siteLogo = computed(() => appStore.cachedPublicSettings?.site_logo || appStore.siteLogo || '')
const docUrl = computed(() => appStore.cachedPublicSettings?.doc_url || appStore.docUrl || '')
const homeContent = computed(() => appStore.cachedPublicSettings?.home_content || '')

// Check if homeContent is a URL (for iframe display)
const isHomeContentUrl = computed(() => {
  const content = homeContent.value.trim()
  return content.startsWith('http://') || content.startsWith('https://')
})

// Theme
const isDark = ref(document.documentElement.classList.contains('dark'))

// GitHub URL
const githubUrl = 'https://github.com/Wei-Shaw/sub2api'

// Auth state
const isAuthenticated = computed(() => authStore.isAuthenticated)
const isAdmin = computed(() => authStore.isAdmin)
const dashboardPath = computed(() => isAdmin.value ? '/admin/dashboard' : '/dashboard')
const userInitial = computed(() => {
  const user = authStore.user
  if (!user || !user.email) return ''
  return user.email.charAt(0).toUpperCase()
})

// Current year for footer
const currentYear = computed(() => new Date().getFullYear())

// Random latency for terminal animation
const latency = ref(73)
const tokens = ref(1271)
function updateLatency() {
  latency.value = Math.floor(Math.random() * 400) + 50 // Random between 50-450ms
  tokens.value = Math.floor(Math.random() * 2000) + 500 // Random between 500-2500 tokens
}

// Comparison table data
const comparisonItems = computed(() => [
  { feature: t('home.comparison.rows.pricing'), official: t('home.comparison.rows.pricingOfficial'), us: t('home.comparison.rows.pricingUs') },
  { feature: t('home.comparison.rows.models'), official: t('home.comparison.rows.modelsOfficial'), us: t('home.comparison.rows.modelsUs') },
  { feature: t('home.comparison.rows.management'), official: t('home.comparison.rows.mgmtOfficial'), us: t('home.comparison.rows.mgmtUs') },
  { feature: t('home.comparison.rows.stability'), official: t('home.comparison.rows.stabOfficial'), us: t('home.comparison.rows.stabUs') },
  { feature: t('home.comparison.rows.billing'), official: t('home.comparison.rows.billingOfficial'), us: t('home.comparison.rows.billingUs') },
])

// Toggle theme
function toggleTheme() {
  isDark.value = !isDark.value
  document.documentElement.classList.toggle('dark', isDark.value)
  localStorage.setItem('theme', isDark.value ? 'dark' : 'light')
}

// Initialize theme
function initTheme() {
  const savedTheme = localStorage.getItem('theme')
  if (
    savedTheme === 'dark' ||
    (!savedTheme && window.matchMedia('(prefers-color-scheme: dark)').matches)
  ) {
    isDark.value = true
    document.documentElement.classList.add('dark')
  }
}

onMounted(() => {
  initTheme()

  // Check auth state
  authStore.checkAuth()

  // Ensure public settings are loaded (will use cache if already loaded from injected config)
  if (!appStore.publicSettingsLoaded) {
    appStore.fetchPublicSettings()
  }

  // Update latency every 7 seconds
  setInterval(updateLatency, 7000)
})
</script>

<style scoped>
/* Terminal Container */
.terminal-container {
  position: relative;
  display: inline-block;
}

/* Terminal Window */
.terminal-window {
  width: 420px;
  background: linear-gradient(145deg, #1e293b 0%, #0f172a 100%);
  border-radius: 14px;
  box-shadow:
    0 25px 50px -12px rgba(0, 0, 0, 0.4),
    0 0 0 1px rgba(255, 255, 255, 0.1),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
  overflow: hidden;
  transform: perspective(1000px) rotateX(2deg) rotateY(-2deg);
  transition: transform 0.3s ease;
  animation: terminal-pulse 6s ease-in-out infinite;
  position: relative;
}

/* CRT scanlines overlay */
.terminal-window::after {
  content: '';
  position: absolute;
  inset: 0;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0, 0, 0, 0.03) 2px,
    rgba(0, 0, 0, 0.03) 4px
  );
  pointer-events: none;
  z-index: 10;
}

.terminal-window:hover {
  transform: perspective(1000px) rotateX(0deg) rotateY(0deg) translateY(-4px);
  animation-play-state: paused;
}

@keyframes terminal-pulse {
  0%, 100% {
    box-shadow:
      0 25px 50px -12px rgba(0, 0, 0, 0.4),
      0 0 0 1px rgba(6, 182, 212, 0.1),
      0 0 10px rgba(6, 182, 212, 0.05);
  }
  50% {
    box-shadow:
      0 25px 50px -12px rgba(0, 0, 0, 0.4),
      0 0 0 1px rgba(6, 182, 212, 0.25),
      0 0 25px rgba(6, 182, 212, 0.15);
  }
}

/* Terminal Header */
.terminal-header {
  display: flex;
  align-items: center;
  padding: 12px 16px;
  background: rgba(30, 41, 59, 0.8);
  border-bottom: 1px solid rgba(255, 255, 255, 0.05);
}

.terminal-buttons {
  display: flex;
  gap: 8px;
}

.terminal-buttons span {
  width: 12px;
  height: 12px;
  border-radius: 50%;
}

.btn-close {
  background: #ef4444;
}
.btn-minimize {
  background: #eab308;
}
.btn-maximize {
  background: #22c55e;
}

.terminal-title {
  flex: 1;
  text-align: center;
  font-size: 12px;
  font-family: ui-monospace, monospace;
  color: #64748b;
  margin-right: 52px;
}

/* Terminal Body */
.terminal-body {
  padding: 20px 24px;
  font-family: ui-monospace, 'Fira Code', monospace;
  font-size: 14px;
  line-height: 2;
}

.code-line {
  display: flex;
  align-items: center;
  gap: 8px;
}

.code-prompt {
  color: #22c55e;
}
.code-method {
  color: #38bdf8;
}
.code-success {
  color: #22c55e;
}
.code-latency {
  color: #fbbf24;
}
.code-separator {
  color: #64748b;
}
.code-tokens {
  color: #94a3b8;
}

/* Provider Card - breathing glow animation */
.provider-card {
  animation: breathe 3s ease-in-out infinite;
}
.provider-card:nth-child(2) {
  animation-delay: 0.5s;
}
.provider-card:nth-child(3) {
  animation-delay: 1s;
}
.provider-card:nth-child(4) {
  animation-delay: 1.5s;
}

@keyframes breathe {
  0%, 100% {
    box-shadow: 0 0 0 0 rgba(6, 182, 212, 0);
  }
  50% {
    box-shadow: 0 0 20px 2px rgba(6, 182, 212, 0.15);
  }
}

/* Stats Container - fade in and glow animation */
.stats-container {
  animation: stats-fade-in 0.8s ease-out;
}

@keyframes stats-fade-in {
  0% {
    opacity: 0;
    transform: translateY(10px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Stat Item - staggered entrance animation */
.stat-item {
  animation: stat-item-appear 0.6s ease-out forwards;
  opacity: 0;
  transform: translateY(15px);
}

.stat-item:nth-child(1) {
  animation-delay: 0.2s;
}

.stat-item:nth-child(2) {
  animation-delay: 0.4s;
}

.stat-item:nth-child(3) {
  animation-delay: 0.6s;
}

@keyframes stat-item-appear {
  0% {
    opacity: 0;
    transform: translateY(15px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Stat Number - pulse animation */
.stat-number {
  animation: stat-number-pulse 2s ease-in-out infinite;
  text-shadow: 0 0 10px currentColor;
}

@keyframes stat-number-pulse {
  0%, 100% {
    transform: scale(1);
    text-shadow: 0 0 10px currentColor;
  }
  50% {
    transform: scale(1.05);
    text-shadow: 0 0 20px currentColor, 0 0 30px currentColor;
  }
}

/* Stat Bar - fill animation with glow effect */
.stat-bar {
  animation: stat-bar-fill 2s cubic-bezier(0.4, 0, 0.2, 1) forwards;
  transform-origin: left;
  transform: scaleX(0);
  box-shadow: 0 0 10px currentColor, 0 0 20px currentColor;
  filter: brightness(1.2);
}

.stat-bar-cyan {
  animation-delay: 0.8s;
}

.stat-bar-green {
  animation-delay: 1s;
}

.stat-bar-amber {
  animation-delay: 1.2s;
}

@keyframes stat-bar-fill {
  0% {
    transform: scaleX(0);
    opacity: 0.8;
  }
  50% {
    opacity: 1;
  }
  100% {
    transform: scaleX(1);
    opacity: 1;
  }
}

/* Stat Bar Container - glow on hover */
.stat-item:hover .stat-bar-container {
  box-shadow: 0 0 15px rgba(6, 182, 212, 0.3);
  transition: box-shadow 0.3s ease;
}

.stat-item:hover .stat-bar-cyan {
  box-shadow: 0 0 10px rgba(6, 182, 212, 0.6);
}

.stat-item:hover .stat-bar-green {
  box-shadow: 0 0 10px rgba(34, 197, 94, 0.6);
}

.stat-item:hover .stat-bar-amber {
  box-shadow: 0 0 10px rgba(251, 191, 36, 0.6);
}

/* Feature Card - glowing border on hover */
.feature-card:hover {
  box-shadow: 0 0 0 1px rgba(6, 182, 212, 0.5), 0 0 30px rgba(6, 182, 212, 0.15);
}

/* Light mode terminal */
.terminal-window {
  box-shadow:
    0 25px 50px -12px rgba(0, 0, 0, 0.3),
    0 0 0 1px rgba(6, 182, 212, 0.2),
    0 0 40px rgba(6, 182, 212, 0.1);
}

/* Dark mode terminal */
:deep(.dark) .terminal-window {
  box-shadow:
    0 25px 50px -12px rgba(0, 0, 0, 0.7),
    0 0 0 1px rgba(6, 182, 212, 0.3),
    0 0 50px rgba(6, 182, 212, 0.15),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
}
</style>
