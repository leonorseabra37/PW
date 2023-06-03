<template>
  <div>
    <!-- Stats cards -->
    <div class="row">
      <div class="col-md-6 col-xl-3" v-for="stats in statsCards" :key="stats.title">
        <stats-card>
          <div class="icon-big text-center" :class="`icon-${stats.type}`" slot="header">
            <i :class="stats.icon"></i>
          </div>
          <div class="numbers" slot="content">
            <p>{{ stats.title }}</p>
            {{ stats.value }}
          </div>
          <div class="stats" slot="footer">
            <i :class="stats.footerIcon"></i> {{ stats.footerText }}
          </div>
        </stats-card>
      </div>
    </div>

    <!-- Charts -->
    <div class="row">
      <div class="col-12">
        <chart-card
          title="Utilizadores Logados"
          sub-title="24 Horss performance"
          :chart-data="usersChart.data"
          :chart-options="usersChart.options"
        >
          <span slot="footer">
            <i class="ti-reload"></i> Updated 3 minutes ago
          </span>
          <div slot="legend">
            <i class="fa fa-circle text-info"></i> Open
            <i class="fa fa-circle text-danger"></i> Click
            <i class="fa fa-circle text-warning"></i> Click Second Time
          </div>
        </chart-card>
      </div>

      <div class="col-md-6 col-12">
        <chart-card
          title="Estatísticas Campo"
          sub-title="Last campaign performance"
          :chart-data="preferencesChart.data"
          chart-type="Pie"
        >
          <span slot="footer">
            <i class="ti-timer"></i> Campaign set 2 days ago
          </span>
          <div slot="legend">
            <i class="fa fa-circle text-info"></i> Campo 1
            <i class="fa fa-circle text-danger"></i> Campo 2
            <i class="fa fa-circle text-warning"></i> Campo 3
            <i class="fa fa-circle text-wa"></i> Campo 4
          </div>
        </chart-card>
      </div>

      <div class="col-md-6 col-12">
        <chart-card
          title="Horas Mais Concorridas"
          sub-title="All products including Taxes"
          :chart-data="activityChart.data"
          :chart-options="activityChart.options"
        >
          <span slot="footer">
            <i class="ti-check"></i> Data information certified
          </span>
          <div slot="legend">
            <i class="fa fa-circle text-info"></i> Clientes
          </div>
        </chart-card>
      </div>
    </div>
  </div>
</template>

<script>
import { StatsCard, ChartCard } from "@/components/index";
import Chartist from "chartist";

export default {
  components: {
    StatsCard,
    ChartCard,
  },
  data() {
    return {
      statsCards: [
        {
          type: "warning",
          icon: "ti-server",
          title: "Reservas Campo",
          value: 0,
          footerText: "Updated now",
          footerIcon: "ti-reload",
        },
        {
          type: "success",
          icon: "ti-wallet",
          title: "Dinheiro de Reservas",
          value: 0,
          footerText: "Last day",
          footerIcon: "ti-calendar",
        },
        {
          type: "danger",
          icon: "ti-pulse",
          title: "Aulas",
          value: 0,
          footerText: "In the last hour",
          footerIcon: "ti-timer",
        },
        {
          type: "info",
          icon: "ti-twitter-alt",
          title: "Utilizadores",
          value: 0,
          footerText: "Updated now",
          footerIcon: "ti-reload",
        },
      ],
      usersChart: {
        data: {
          labels: [
            "9:00AM",
            "12:00AM",
            "3:00PM",
            "6:00PM",
            "9:00PM",
            "12:00PM",
            "3:00AM",
            "6:00AM",
          ],
          series: [
            [287, 385, 490, 562, 594, 626, 698, 895, 952],
            [67, 152, 193, 240, 387, 435, 535, 642, 744],
            [23, 113, 67, 108, 190, 239, 307, 410, 410],
          ],
        },
        options: {
          low: 0,
          high: 1000,
          showArea: true,
          height: "245px",
          axisX: {
            showGrid: false,
          },
          lineSmooth: Chartist.Interpolation.simple({
            divisor: 3,
          }),
          showLine: true,
          showPoint: false,
        },
      },
      activityChart: {
        data: {
          labels: [
            "9:00AM",
            "11:00AM",
            "1:00PM",
            "3:00PM",
            "5:00PM",
            "7:00PM",
            "9:00PM",
            "11:00PM",
          ],
          series: [
            [542, 543, 520, 680, 653, 753, 326, 434, 568, 610, 756, 895],
          ],
        },
        options: {
          seriesBarDistance: 10,
          axisX: {
            showGrid: false,
          },
          height: "245px",
        },
      },
      preferencesChart: {
        data: {
          labels: ["Campo 1", "Campo 2", "Campo 3", "Campo 4"],
          series: [0, 0, 0, 0],
        },
        options: {},
      },
    };
  },
  
  mounted() {
    this.updateReservasAceite();
    this.carregarNumeroReservasCampo();
    this.calcularDinheiroReservas();
    this.calcularNumeroAulas();
    this.carregarDadosLocalStorage();
  },
  methods: {
    updateReservasAceite() {
      const reservasAceiteLocalStorage = localStorage.getItem("reservaAceite");
      if (reservasAceiteLocalStorage) {
        const reservasAceite = JSON.parse(reservasAceiteLocalStorage);
        const reservasCampoCard = this.statsCards.find(
          (card) => card.title === "Reservas Campo"
        );
        if (reservasCampoCard) {
          reservasCampoCard.value = reservasAceite.length;
        }
      }
    },
    calcularDinheiroReservas() {
      const reservasLocalStorage = JSON.parse(localStorage.getItem("reservas"));
      if (reservasLocalStorage) {
        const dinheiroReservasCard = this.statsCards.find(
          (card) => card.title === "Dinheiro de Reservas"
        );
        if (dinheiroReservasCard) {
          const totalDinheiro = reservasLocalStorage.reduce(
            (total, reserva) => total + reserva.valor,
            0
          );
          dinheiroReservasCard.value = totalDinheiro.toFixed(2);
        }
      }
    },
    calcularNumeroAulas() {
      const aulasLocalStorage = JSON.parse(localStorage.getItem("aulas"));
      if (aulasLocalStorage) {
        const aulasCard = this.statsCards.find((card) => card.title === "Aulas");
        if (aulasCard) {
          aulasCard.value = aulasLocalStorage.length;
        }
      }
    },
    carregarNumeroReservasCampo() {
      const reservasLocalStorage = JSON.parse(localStorage.getItem("reservas"));
      if (reservasLocalStorage) {
        const reservasCampoCard = this.statsCards.find(
          (card) => card.title === "Reservas Campo"
        );
        if (reservasCampoCard) {
          const numeroReservasCampo = reservasLocalStorage.filter(
            (reserva) => reserva.tipo === "campo"
          ).length;
          reservasCampoCard.value = numeroReservasCampo;
        }
      }
    },
    carregarDadosLocalStorage() {
      const utilizadoresLocalStorage = JSON.parse(
        localStorage.getItem("utilizadores")
      );
      if (utilizadoresLocalStorage) {
        const utilizadoresCard = this.statsCards.find(
          (card) => card.title === "Utilizadores"
        );
        if (utilizadoresCard) {
          utilizadoresCard.value = utilizadoresLocalStorage.length;
        }
      }
    },
  },
};
</script>

<style scoped>
.row {
  margin-left: 0;
  margin-right: 0;
}
.icon-big {
  font-size: 3em !important;
}
.numbers {
  text-align: right;
}
.stats {
  color: #999;
}
</style>
