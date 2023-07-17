<template>
  <div class="absolute right-0 top-0 m-4 text-yellow-500 dark:text-yellow-300">
    <ColorScheme placeholder="..." tag="span">
      <Icon
        v-if="$colorMode.value === 'light'"
        class="h-8 w-8 cursor-pointer md:h-12 md:w-12"
        name="material-symbols:light-mode"
        @click="$colorMode.preference = 'dark'"
      />
      <Icon
        v-else-if="$colorMode.value === 'dark'"
        class="h-8 w-8 cursor-pointer md:h-12 md:w-12"
        name="material-symbols:dark-mode"
        @click="$colorMode.preference = 'light'"
      />
    </ColorScheme>
  </div>
</template>
