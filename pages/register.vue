<template>
  <section>
    <h1 class="title">Formulário de uso da lan house</h1>
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
          step="30"
          :min="0"
        ></v-text-field>
        <v-select
          v-model="register.computerId"
          :items="availableComputers"
          item-value="id"
          item-text="machineName"
          label="Selecione o computador"
        ></v-select>
        <v-btn class="mt-2" type="submit" block color="blue">Registrar</v-btn>
      </v-form>
    </v-sheet>
    <v-snackbar v-model="isItSuccessful" :timeout="1000" top>
      {{ isItSuccessful ? 'Registro feito com sucesso!' : 'Falha ao registrar' }}
    </v-snackbar>
  </section>
</template>

<script>
export default {
  data() {
    return {
      register: {
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
    await this.fetchComputers();
  },
  computed: {
    availableComputers() {
      return this.computers.filter(computer => !computer.isItBeingUsed);
    }
  },
  methods: {
    async fetchComputers() {
      try {
        const response = await this.$axios.get('https://localhost:7004/Computer');
        this.computers = response.data;
      } catch (error) {
        console.error('Erro ao buscar computadores:', error.message);
      }
    },
    async submit() {
      try {
        if (!this.register.user.name || !this.register.timeBeingUsed || !this.register.computerId) {
          throw new Error('Por favor, preencha todos os campos obrigatórios.');
        }

        const startUsingPc = new Date();
        const endUsingPc = new Date(startUsingPc.getTime() + this.register.timeBeingUsed * 60000);
        const startUsingPcBrazil = startUsingPc.toLocaleString('pt-BR', { timeZone: 'America/Sao_Paulo' });
        const endUsingPcBrazil = endUsingPc.toLocaleString('pt-BR', { timeZone: 'America/Sao_Paulo' });

        this.register.startUsingPc = new Date(startUsingPcBrazil).toISOString();
        this.register.endUsingPc = new Date(endUsingPcBrazil).toISOString();

        const response = await fetch('https://localhost:7004/api/Register', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            timeBeingUsed: this.register.timeBeingUsed,
            startUsingPc: this.register.startUsingPc,
            endUsingPc: this.register.endUsingPc,
            computerId: this.register.computerId,
            userId: this.register.userId,
            user: { name: this.register.user.name.toUpperCase() },
            computer: { id: this.register.computerId, machineName: '', isItBeingUsed: true },
          })
        });

        const data = await response.json();

        this.register = {
          timeBeingUsed: '',
          startUsingPc: '',
          endUsingPc: '',
          computerId: '',
          user: { name: '' }
        };

        if (response.ok) {
          this.isItSuccessful = 'Registro feito com sucesso!';
        } else {
          const errorData = await response.json();
          this.isItSuccessful = `Falha ao registrar: ${errorData.message || response.statusText}`;
        }

      } catch (error) {
        console.error('Erro ao registrar:', error.message);
      }
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
