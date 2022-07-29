<template>
  <div :class="estilo" role="alert">
    {{ msg }}
    <hr />
    <p>{{ detalhes.mensagem }}</p>
    <br />
    <ul v-if="detalhes.dados">
      <li v-for="e, key in detalhes.dados" :key="key">{{ e[0] }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  props: ["msg", "tipo", "detalhes"],
  computed: {
    estilo() {
      return "alert alert-" + this.tipo;
    },
  },
};
</script>
