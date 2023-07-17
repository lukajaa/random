<template :class="$colorMode.value">
  <div class="bg-slate-50 transition duration-300 dark:bg-slate-800 dark:text-slate-50">
    <ThemeSwitcher />
    <slot />
    <FooterComponent />
  </div>
</template>
