<template>
  <div class="items-center justify-between text-center my-2">
    <h1 class="text-2xl font-bold text-teal-800">Post Test Data Visualization - Day 2</h1>
    <p class="mt-2">Tazkia Athariza Dhivara</p>
  </div>
 <div class="flex my-1 mx-auto p-5 items-center justify-between lg:mx-20">
   <div class="my-5 mx-5">
        <p class="text-teal-800 mb-4">✓ Multiple chart (bar, pie, and line) & multiple axis ('jumlah Penduduk' and 'jumlah penduduk per km<sup>2</sup>')</p>
        <highchart 
        :options="chartOptions"   
        :modules="['exporting', 'export-data']"
        class="p-5 bg-gray-100 border rounded-2xl hover:border-teal-500 hover:border-1 hover:shadow-md transition duration-300 ease-in-out"
        />
        </div>
    </div>
        
</template>

<script>
definePageMeta({ 
  layout: 'forchart',
});

export default {
  data() {
    return {
      items: [],
      chartOptions: {
        chart: {
          width: 920, 
          height: 900, 
          backgroundColor: 'transparent',
        },
        title: {
          text: "Statistik Penduduk Indonesia pada Tahun 2023",
          align: 'left',
        },
        subtitle: {
          text: "Data Penduduk Indonesia pada Tahun 2023 Berdasarkan Provinsi",
          align: 'left',
        },
        legend: {
          align: 'left',
          verticalAlign: 'top',
          layout: 'horizontal', 
        },
        xAxis: {
            title: {
                text: 'PROVINSI',
            },
            categories: [],
        },
        yAxis: [
          { // untuk bar chart
            title: {
              text: 'JUMLAH PENDUDUK (jiwa)',
            },
            opposite: true
          },
          { // untuk line
            title: {
              text: "JUMLAH PENDUDUK PER KM2"
            },
            labels: {
              format: '{value} jiwa'
            }
          },
        ],
        series: [
          { // chart 1 : bar
            name: 'Jumlah Penduduk per Provinsi',
            type: 'bar',
            data: [],
            tooltip: {
              headerFormat: '<b>Provinsi: {point.x}</b><br/>',
              pointFormat: 'Jumlah penduduk: <b>{point.y} jiwa</b><br/>',
            },
          },
          { // chart 2 : pie
            name: 'Total',
            type: 'pie',
            size: '200',
            center: [560, 75],
            innerSize: '20%',
            data: [],
            tooltip: {
              pointFormat: '<b><point.name></b>Jumlah penduduk: <b>{point.y} </b> jiwa',
            },
          },
          { // chart 3 : line
            name: 'Jumlah Penduduk per km2',
            type: 'line',
            data: [],
            tooltip: {
              headerFormat: '<b>Provinsi: {point.x}</b><br/>',
              pointFormat: 'Jumlah penduduk per km2: <b>{point.y} jiwa/km2</b><br/>',
            },
            yAxis: 1,
          },
        ],
        tooltip: { 
          enabled: true, 
          shared: true, 
        }, 
         plotOptions: {
          pie: {
            allowPointSelect: true,
            cursor: 'pointer',
            dataLabels: {
              enabled: true,
              distance: -50,
              format: '{point.name}',
            },
          },
        },
      },
    };
  },
  mounted() {
    this.fetchPeopleDataList();
  },
  methods: {
    hurufKapital(provinsi) { // huruf kapital untuk provinsii
      return provinsi.split(' ').map(huruf => {
          return huruf.charAt(0).toUpperCase() + huruf.slice(1).toLowerCase();
      }).join(' ');
    },
    async fetchPeopleDataList() {
      const resp = await fetch('https://api-e-database.kemendagri.go.id/api/dukcapil_jumlah_penduduk_prov/51F890E2DF?tahun=2023');
      const body = await resp.json();
      this.items = body.data;
      console.log(body);
      
      // Masukan data dari API ke xAxis
      this.chartOptions.xAxis.categories = this.items.map(entry => this.hurufKapital(entry.prov));
      console.log("ini xAxis", this.chartOptions.xAxis.categories);

      // Masukan data dari API untuk bar chart
      this.chartOptions.series[0].data = this.items.map(entry => parseFloat(entry.jumlah_penduduk)); // ubahtz string ke angka
      console.log("ini series untuk bar", this.chartOptions.series[0].data);

      // Masukan data dari API untuk pie chart
      this.chartOptions.series[1].data = this.items.map(entry => ({
        name: entry.prov,
        y: parseFloat(entry.jumlah_penduduk),
      }));

      // Masukan data dari API untuk line chart
      this.chartOptions.series[2].data = this.items.map(entry => parseFloat(entry.jumlah_penduduk_km2));
       console.log("ini series untuk line", this.chartOptions.series[2].data);
    },
 
  },
};
</script>