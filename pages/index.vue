<template>
  <v-data-table
    :headers="headers"
    :items="registers"
    item-key="id"
    hide-default-footer
  >
    <template v-slot:item.delete="{ item }">
      <v-btn @click="deleteRegister(item.id)" color="red" icon>
        <v-icon>mdi-delete</v-icon>
      </v-btn>
    </template>

    <template v-slot:item.update="{ item }">
      <v-btn @click="goToUpdate(item.id)" color="blue" icon>
        <v-icon>mdi-update</v-icon>
      </v-btn>
    </template>

  </v-data-table>
</template>

<script>
export default {
  data() {
    return {
      registers: [],
      headers: [
        { text: 'Usuário', value: 'user.name' },
        { text: 'Tempo (minutos)', value: 'timeBeingUsed' },
        { text: 'Computador', value: 'computer.machineName' },
        { text: 'Hora inicial', value: 'startUsingPc', align: 'center' },
        { text: 'Hora final', value: 'endUsingPc', align: 'center' },
        { text: 'Excluir', value: 'delete', align: 'center', sortable: false },
        { text: 'Atualizar', value: 'update', align: 'center', sortable: false }
      ]
    };
  },
  async created() {
    await this.fetchRegisters();
  },
  methods: {
    async fetchRegisters() {
      try {
        const response = await this.$axios.get('https://localhost:7004/api/Register');
        this.registers = response.data;
      } catch (error) {
        console.error('Erro ao buscar registros:', error.message);
      }
    },
    async deleteRegister(registerId) {
      try {
        await this.$axios.delete(`https://localhost:7004/api/Register/${registerId}`);
        await this.fetchRegisters();
      } catch (error) {
        console.error('Erro ao excluir o registro:', error.message);
      }
    },

    async goToUpdate(registerId) {
      try{
        await this.$router.push({ name: 'update', params: { id: registerId } });
      }catch (error) {
        console.error('Erro:', error.message);
      }

    }
  }
};
</script>

<style scoped>
  template {
    background-color: #F44336 !important;
  }
</style>
