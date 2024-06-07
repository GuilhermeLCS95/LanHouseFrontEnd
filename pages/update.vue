<template>
  <section>
    <h1 class="title">Atualizar Registro</h1>
    <v-sheet class="mx-auto form">
      <v-form @submit.prevent="submit">
        <v-text-field
          v-model="register.user.name"
          label="Nome do usuário"
        ></v-text-field>
        <v-text-field
          v-model.number="register.timeBeingUsed"
          type="number"
          label="Tempo que usará o computador (em minutos)"
          :min="0"
          step="30"
        ></v-text-field>
        <v-select
          v-model="register.computerId"
          :items="availableComputers"
          item-value="id"
          item-text="machineName"
          label="Selecione o computador"
        ></v-select>
        <v-btn class="mt-2" type="submit" block color="blue">Atualizar</v-btn>
        <v-btn class="mt-2" block @click="goBack" color="red">Voltar</v-btn>
      </v-form>
    </v-sheet>
    <v-snackbar v-model="isItSuccessful" :timeout="3000" top>
      {{ isItSuccessful ? 'Atualização feita com sucesso!' : 'Falha ao atualizar' }}
    </v-snackbar>
  </section>
</template>

<script>
export default {
  data() {
    return {
      register: {
        id: '',
        timeBeingUsed: '',
        startUsingPc: '',
        endUsingPc: '',
        computerId: '',
        user: { name: '' }
      },
      computers: [],
      isItSuccessful: '',
    };
  },
  async created() {
    const registerId = this.$route.params.id;
    await this.fetchComputers();
    await this.fetchRegister(registerId);
  },
  computed: {
    availableComputers() {
      return this.computers.filter(computer => !computer.isItBeingUsed || computer.id === this.register.computerId);
    }
  },
  methods: {
    async fetchComputers() {
      try {
        const response = await this.$axios.get('https://localhost:7004/Computer/');
        this.computers = response.data;
      } catch (error) {
        console.error('Erro ao buscar computadores:', error.message);
      }
    },
    async fetchRegister(id) {
      try {
        const response = await this.$axios.get(`https://localhost:7004/api/Register/${id}`);
        this.register = response.data;
      } catch (error) {
        console.error('Erro ao buscar registro:', error.message);
      }
    },
    async submit() {
      try {
        if (!this.register.user.name || !this.register.timeBeingUsed || !this.register.computerId) {
          throw new Error('Por favor, preencha todos os campos obrigatórios.');
        }

        const startUsingPc = new Date(this.register.startUsingPc);
        const endUsingPc = new Date(startUsingPc.getTime() + this.register.timeBeingUsed * 60000);
        const startUsingPcBrazil = startUsingPc.toLocaleString('pt-BR', { timeZone: 'America/Sao_Paulo' });
        const endUsingPcBrazil = endUsingPc.toLocaleString('pt-BR', { timeZone: 'America/Sao_Paulo' });

        this.register.startUsingPc = new Date(startUsingPcBrazil).toISOString();
        this.register.endUsingPc = new Date(endUsingPcBrazil).toISOString();

        const response = await this.$axios.put(`https://localhost:7004/api/Register/${this.register.id}`, {
          id: this.register.id,
          startUsingPc: this.register.startUsingPc,
          endUsingPc: this.register.endUsingPc,
          timeBeingUsed: this.register.timeBeingUsed,
          computerId: this.register.computerId,
          user: { name: this.register.user.name },
          computer: { id: this.register.computerId, machineName: '', isItBeingUsed: true },
        });

        this.isItSuccessful = 'Registro atualizado com sucesso';

        setTimeout(() => {
          this.isItSuccessful = '';
          this.$router.push('/');
        }, 1000);

      } catch (error) {
        console.error('Erro ao atualizar registro:', error.message);
        this.isItSuccessful = 'Falha ao atualizar o registro';
      }

    },
    goBack() {
      this.$router.go(-1);
    }
  }
};
</script>

<style>
.form {
  padding: 20px;
  border-radius: 15px;
}

.title {
  width: 100%;
  text-align: center;
  margin-bottom: 20px;
}
</style>
