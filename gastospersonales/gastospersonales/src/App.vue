<template>
  <div class="container">

    <header>
      <h1> Control de Gastos Personales</h1>
      <p class="subtitulo">Registra y administra tus gastos del mes</p>
    </header>

    <section class="formulario">
      <h2>Agregar Gasto</h2>

      <div class="campo">
        <label for="descripcion">Descripción:</label>
        <input
          id="descripcion"
          type="text"
          v-model="descripcion"
          placeholder="Ej: Supermercado, transporte..."
          :class="{ 'input-error': errores.descripcion }"
        />
        <span v-if="errores.descripcion" class="error">{{ errores.descripcion }}</span>
      </div>

      <div class="campo">
        <label for="monto">Monto ($):</label>
        <input
          id="monto"
          type="number"
          v-model="monto"
          placeholder="0.00"
          :class="{ 'input-error': errores.monto }"
        />
        <span v-if="errores.monto" class="error">{{ errores.monto }}</span>
      </div>

      <div class="campo">
        <label for="categoria">Categoría:</label>
        <select id="categoria" v-model="categoria" :class="{ 'input-error': errores.categoria }">
          <option value="">-- Selecciona una categoría --</option>
          <option v-for="cat in categorias" :key="cat" :value="cat">{{ cat }}</option>
        </select>
        <span v-if="errores.categoria" class="error">{{ errores.categoria }}</span>
      </div>

      <button @click="agregarGasto" class="btn-agregar"> Agregar Gasto</button>
    </section>

    <section class="resumen" v-if="gastos.length > 0">
      <h2>📊 Resumen</h2>
      <p>Total de gastos registrados: <strong>{{ gastos.length }}</strong></p>
      <p>Total gastado: <strong>${{ totalGastado.toFixed(2) }}</strong></p>
      <p v-if="totalGastado > presupuesto" class="alerta">
         ¡Has superado tu presupuesto mensual de ${{ presupuesto }}!
      </p>
      <p v-else class="bien">
         Estás dentro del presupuesto. Te quedan ${{ (presupuesto - totalGastado).toFixed(2) }}
      </p>
    </section>

    <section class="lista" v-if="gastos.length > 0">
      <h2> Lista de Gastos</h2>
      <table>
        <thead>
          <tr>
            <th>#</th>
            <th>Descripción</th>
            <th>Categoría</th>
            <th>Monto</th>
            <th>Acción</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(gasto, index) in gastos" :key="index">
            <td>{{ index + 1 }}</td>
            <td>{{ gasto.descripcion }}</td>
            <td>
              <span class="badge">{{ gasto.categoria }}</span>
            </td>
            <td>${{ parseFloat(gasto.monto).toFixed(2) }}</td>
            <td>
              <button @click="eliminarGasto(index)" class="btn-eliminar"> Eliminar</button>
            </td>
          </tr>
        </tbody>
      </table>
    </section>

    <section v-if="gastos.length === 0" class="vacio">
      <p> Aún no has registrado ningún gasto. ¡Empieza agregando uno!</p>
    </section>

  </div>
  
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      descripcion: '',
      monto: '',
      categoria: '',
      presupuesto: 500,
      gastos: [],
      errores: {
        descripcion: '',
        monto: '',
        categoria: ''
      },
      categorias: [
        'Alimentación',
        'Transporte',
        'Salud',
        'Entretenimiento',
        'Educación',
        'Servicios',
        'Otros'
      ]
    }
  },
  computed: {
    totalGastado() {
      return this.gastos.reduce((acc, g) => acc + parseFloat(g.monto), 0)
    }
  },
  methods: {
    validar() {
      let valido = true
      this.errores = { descripcion: '', monto: '', categoria: '' }

      if (!this.descripcion.trim()) {
        this.errores.descripcion = 'La descripción es obligatoria.'
        valido = false
      } else if (this.descripcion.trim().length < 3) {
        this.errores.descripcion = 'La descripción debe tener al menos 3 caracteres.'
        valido = false
      }

      if (!this.monto) {
        this.errores.monto = 'El monto es obligatorio.'
        valido = false
      } else if (isNaN(this.monto) || parseFloat(this.monto) <= 0) {
        this.errores.monto = 'Ingresa un monto válido mayor a 0.'
        valido = false
      }

      if (!this.categoria) {
        this.errores.categoria = 'Debes seleccionar una categoría.'
        valido = false
      }

      return valido
    },

    agregarGasto() {
      if (!this.validar()) return
      this.gastos.push({
        descripcion: this.descripcion.trim(),
        monto: this.monto,
        categoria: this.categoria
      })
      this.descripcion = ''
      this.monto = ''
      this.categoria = ''
    },

    eliminarGasto(index) {
      this.gastos.splice(index, 1)
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Segoe UI', sans-serif;
  background-color: #f0f4f8;
  color: #333;
}

.container {
  max-width: 800px;
  margin: 30px auto;
  padding: 20px;
}

header {
  background: linear-gradient(135deg, #1e3a5f, #2e86de);
  color: white;
  padding: 30px;
  border-radius: 12px;
  text-align: center;
  margin-bottom: 24px;
}

header h1 { font-size: 2rem; }
.subtitulo { margin-top: 6px; opacity: 0.85; }

section {
  background: white;
  border-radius: 12px;
  padding: 24px;
  margin-bottom: 20px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.08);
}

h2 {
  font-size: 1.2rem;
  margin-bottom: 16px;
  color: #1e3a5f;
  border-bottom: 2px solid #e2e8f0;
  padding-bottom: 8px;
}

.campo {
  margin-bottom: 16px;
  display: flex;
  flex-direction: column;
  gap: 6px;
}

label { font-weight: 600; font-size: 0.9rem; }

input, select {
  padding: 10px 14px;
  border: 2px solid #cbd5e0;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.2s;
}

input:focus, select:focus {
  outline: none;
  border-color: #2e86de;
}

.input-error { border-color: #e53e3e !important; }

.error {
  color: #e53e3e;
  font-size: 0.82rem;
}

.btn-agregar {
  background: #2e86de;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  width: 100%;
  margin-top: 4px;
}
.btn-agregar:hover { background: #1a6fc4; }

.alerta { color: #c53030; font-weight: bold; margin-top: 8px; }
.bien { color: #276749; font-weight: bold; margin-top: 8px; }

table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.95rem;
}

th, td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #e2e8f0;
}

th {
  background: #f7fafc;
  font-weight: 700;
  color: #1e3a5f;
}

tr:hover { background: #f0f8ff; }

.badge {
  padding: 4px 10px;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 600;
  background: #bee3f8;
  color: #2a4a6b;
}

.btn-eliminar {
  background: #fed7d7;
  color: #c53030;
  border: none;
  padding: 6px 12px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 0.85rem;
}
.btn-eliminar:hover { background: #feb2b2; }

.vacio {
  text-align: center;
  color: #718096;
  font-size: 1rem;
  padding: 40px !important;
}
</style>