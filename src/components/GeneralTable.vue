<template>
  <div class="general__table">
    <table>
      <thead>
        <tr>
          <th>Option</th>
          <th>Average</th>
          <th>Max</th>
          <th>Median</th>
          <th>Min</th>
          <th>Stdev</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="metric in tableRows">
          <td>{{ metric[0] }}</td>
          <td>{{ metric[1] }}</td>
          <td>{{ metric[2] }}</td>
          <td>{{ metric[3] }}</td>
          <td>{{ metric[4] }}</td>
          <td>{{ metric[5] }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>


<script>
  export default {
    name: 'GeneralTable',
    data() {
      return {
        tableRows: [],
      }
    },
    props: {
      codecData: {
        type: Object,
        required: true
      }
    },
    created() {
      this.parseDataTable(this.codecData)
    },
    watch: {
      codecData() {
        this.parseDataTable(this.codecData)
      }
    },
    methods: {
      /**
       * Parses the data and pushes it into the table.
       * @param {Object} codecData - Data to be parsed.
       */
       parseDataTable(data) {
        this.tableRows = []

        const psnr = data.quality_metrics.global.psnr.mse_avg
        const ssim = data.quality_metrics.global.ssim.ssim_avg
        const vmaf = data.quality_metrics.global.vmaf.vmaf

        this.tableRows.push(["VMAF", vmaf.average, vmaf.max, vmaf.median, vmaf.min, vmaf.stdev])
        this.tableRows.push(["SSIM", ssim.average, ssim.max, ssim.median, ssim.min, ssim.stdev])
        this.tableRows.push(["PSNR", psnr.average, psnr.max, psnr.median, psnr.min, psnr.stdev])
      },
    }
  }
</script>


<style lang="scss">
  .general__table {
    width: 100%;
    display: flex;
    text-align: center;

    table {
      width: 100%;
      border-collapse: collapse;

      th {
        align-items: center;
        border: 2px solid #000;
        padding: 10px;
        font-weight: bold;
      }

      td {
        align-items: center;
        border: 2px solid #000;
        padding: 10px;
      }
    }
  }

  @media (max-width: 768px) {
    table {
      th, td {
        padding: 8px;
      }
    }
  }

  @media (max-width: 480px) {
    table {
      display: block;

      thead {
        display: none;
      }

      tbody {
        display: block;
      }

      tr {
        display: flex;
        flex-direction: column;
        margin-bottom: 15px;
        border-bottom: 2px solid #000;

        &:last-child {
          border-bottom: none;
        }
      }

      td {
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 8px;
        border: none;
        position: relative;
        padding-left: 50%;

        &:before {
          content: attr(data-label);
          position: absolute;
          left: 10px;
          width: calc(50% - 20px);
          font-weight: bold;
          white-space: nowrap;
          text-align: left;
        }
      }
    }
  }
</style>