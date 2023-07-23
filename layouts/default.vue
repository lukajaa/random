<template :class="$colorMode.value">
  <div class="bg-slate-50 text-gray-950 transition duration-300 dark:bg-gray-950 dark:text-slate-50">
    <ThemeSwitcher />
    <slot />
    <FooterComponent />
  </div>
</template>
