<template>
  <q-page
    class="flex q-mx-auto column "
    style="max-width: 1600px;background-color: #fff;padding-bottom: 5vh;"
  >
    <q-carousel
      animated
      v-model="slide"
      arrows
      navigation
      infinite
      style="width: 100%;max-height: 512px;height: auto;"
    >
      <q-carousel-slide
        :name="i"
        v-for="(image, i) in slideImages"
        :key="image"
        style="padding: 0"
        class="column no-wrap flex-center"
      >
        <q-img :src="image" :ratio="16 / 9" />
      </q-carousel-slide>
    </q-carousel>

    <div
      class="flex q-mx-auto column q-px-lg q-mt-md"
      style="width: 992px;max-width: 100%;"
    >
      <h1 class="text-h4 text-weight-bold" style="color: #2d333f">
        {{ restaurantInfo.name }}
      </h1>

      <div class="text-h6" style="color:#2d333f;margin-bottom: 5px">
        {{ restaurantInfo.address }}
      </div>

      <div class="flex row">
        <div class="flex items-center q-mr-lg">
          <q-icon
            class="q-mr-sm"
            style="color:#2d333f;font-size: 1.3rem;"
            name="call"
          />
          <a
            style="color:#da3743;font-size: 1.2rem;"
            :href="'tel:' + restaurantInfo.phone"
          >
            {{ restaurantInfo.phone }}</a
          >
        </div>
        <div class="flex items-center">
          <q-icon
            class="q-mr-sm"
            style="color:#2d333f;font-size: 1.3rem;"
            name="map"
          />
          <span style="color:#da3743;font-size: 1.2rem;"> 查看地圖</span>
        </div>
      </div>
      <hr />
      <div class="flex row">
        <div class="q-mr-lg">
          <div class="subtitle">用餐人數</div>
          <div class="flex row">
            <q-select
              v-model="form.adult"
              class="q-mr-lg q-mb-sm"
              style="width: 150px;max-width: 45%"
              outlined
              :options="peopleSelect"
            >
              <template v-slot:append>
                <div style="font-size: 16px">位大人</div>
              </template>
            </q-select>

            <q-select
              v-model="form.children"
              class="q-mb-sm"
              style="width: 150px;max-width: 45%"
              outlined
              :options="[0, ...peopleSelect]"
            >
              <template v-slot:append>
                <div style="font-size: 16px">位小孩</div>
              </template>
            </q-select>
          </div>
        </div>
        <div>
          <div class="subtitle">用餐日期</div>
          <div class="flex row">
            <q-select
              v-model="formatDate"
              :options="dateOptions"
              class="q-mr-lg"
              style="width: 16rem"
              outlined
            >
            </q-select>
          </div>
        </div>
      </div>
      <span style="color:#888"
        >*預約人數超過 {{ orderType.maxSeats }} 人時, 請致電聯絡</span
      >
      <hr />
      <div class="subtitle">用餐類型</div>
      <div>
        <q-btn
          unelevated
          size="1.1rem"
          class="q-mr-sm q-px-sm q-mb-sm"
          label="早午餐"
          @click="form.type = 1"
          :outline="form.type != 1"
          :color="form.type === 1 ? 'primary' : 'grey'"
          :disable="isWeekend"
          :class="{
            'text-decoration': isWeekend
          }"
        />
        <q-btn
          unelevated
          size="1.1rem"
          class="q-mr-sm q-px-sm q-mb-sm"
          label="私藏鍋物"
          @click="form.type = 2"
          :outline="form.type != 2"
          :color="form.type === 2 ? 'primary' : 'grey'"
        />
      </div>

      <div class="subtitle q-mt-md">用餐時段</div>
      <template v-if="loading">
        <q-skeleton width="100%" height="12rem" />
      </template>
      <template v-else>
        <!-- <div style="color:#888">
          {{ timeOptions.label }}
        </div> -->
        <div class="q-pa-0 q-mt-sm q-mb-sm">
          <q-btn
            v-for="time in timeOptions.options"
            @click="form.time = time.label"
            :outline="form.time !== time.label"
            :color="form.time === time.label ? 'primary' : 'grey'"
            unelevated
            size="1.1rem"
            class="q-mr-sm q-px-sm q-mb-sm"
            :key="time.label"
            :label="time.label"
            :disable="
              time.value + time.current + form.adult + form.children >
                orderType.maxSeats
            "
            :class="{
              'text-decoration':
                time.value + time.current + form.adult + form.children >
                orderType.maxSeats
            }"
          />
        </div>

        <span style="color:#888">*預約時段只保留10分鐘 有特殊需求請致電</span>
        <span style="color:#888"
          >*如該時段無法選擇, 則表示該時段已額滿, 有特殊需求請致電</span
        >
      </template>
      <hr />

      <div class="subtitle">餐廳資訊</div>

      <restaurant-info />

      <hr />
      <div class="subtitle">菜單</div>

      <q-carousel
        animated
        v-model="slide2"
        infinite
        arrows
        navigation
        style="width: 100%;height: auto;"
      >
        <q-carousel-slide
          :name="i"
          v-for="(image, i) in slideImages2"
          :key="image"
          style="padding: 0;width: 100%"
          class="column no-wrap flex-center"
        >
          <q-img :src="image" :ratio="4 / 3" />
        </q-carousel-slide>
      </q-carousel>
    </div>

    <q-footer style="background: #fff;border-top: 1px solid rgb(232, 232, 232)">
      <div
        class="flex q-px-lg q-py-sm q-mx-auto row"
        style="max-width: 861px;color: #24292e;"
      >
        <q-btn
          color="primary"
          unelevated
          size="1.1rem"
          class="q-mr-sm q-px-sm q-mb-sm full-width"
          label="下一步"
          @click="next"
        />
      </div>
    </q-footer>
  </q-page>
</template>

<script>
import RestaurantInfo from "./components/restaurantInfo";
export default {
  name: "BookingTime",
  components: { RestaurantInfo },
  data() {
    return {
      slideImages: [
        "https://booking.ianleong3712.space/images/S__42328167.jpg",
        "https://booking.ianleong3712.space/images/S__4325406.jpg",
        "https://booking.ianleong3712.space/images/S__42328165.jpg",
        "https://booking.ianleong3712.space/images/S__42319896.jpg",
        "https://booking.ianleong3712.space/images/S__4325385.jpg",
        "https://booking.ianleong3712.space/images/S__4325387.jpg",
        "https://booking.ianleong3712.space/images/S__4325388.jpg",
        "https://booking.ianleong3712.space/images/S__4325389.jpg",
        "https://booking.ianleong3712.space/images/S__4325390.jpg",
        "https://booking.ianleong3712.space/images/S__4325391.jpg",
        "https://booking.ianleong3712.space/images/S__4325392.jpg"
      ],
      slideImages2: [
        "https://booking.ianleong3712.space/images/AD8O3395.jpg",
        "https://booking.ianleong3712.space/images/AD8O3397.jpg",
        "https://booking.ianleong3712.space/images/AD8O3401.jpg",
        "https://booking.ianleong3712.space/images/AD8O3471.jpg",
        "https://booking.ianleong3712.space/images/AD8O3622.jpg",
        "https://booking.ianleong3712.space/images/AD8O3624.jpg",
        "https://booking.ianleong3712.space/images/AD8O3631.jpg"
      ],
      slide: 1,
      slide2: 1,
      loading: true,
      peopleSelect: [],
      booking: {},
      form: {
        type: 2,
        adult: 2,
        children: 0,
        date: this.$dayjs().add(1, "days"),
        time: undefined
      },

      timeOptionsDefault: [
        {
          label: "早午餐",
          maxSeats: 30,
          data: [
            "09:00",
            "09:30",
            "10:00",
            "10:30",
            "11:00",
            "11:30",
            "12:00",
            "12:30",
            "13:00",
            "13:30",
            "14:00",
            "14:30"
          ]
        },
        {
          label: "私藏鍋物",
          maxSeats: 30,
          data: [
            "11:00",
            "11:30",
            "12:00",
            "12:30",
            "13:00",
            "13:30",
            "14:00",
            "14:30",
            "15:00",
            "15:30",
            "16:00",
            "16:30",
            "17:00",
            "17:30",
            "18:00",
            "18:30",
            "19:00",
            "19:30"
          ]
        }
      ]
    };
  },
  async created() {
    const history = {
      id: window.localStorage.getItem("id"),
      phone: window.localStorage.getItem("phone")
    };
    if (history.id && history.phone) {
      try {
        const bookingInfo = await this.getbookingInfo({
          id: history.id,
          phone: history.phone
        });
        this.$router.push({
          name: "BookingOver",
          params: {
            bookingInfo
          }
        });
      } catch (error) {
        window.localStorage.clear("phone");
        window.localStorage.clear("id");
      }
    }

    this.getBooking();

    this.peopleSelect = Array.from(
      { length: this.orderType.maxSeats },
      (_, i) => i + 1
    );
  },
  watch: {
    "form.date"() {
      this.getBooking();
      this.form.type = this.isWeekend ? 2 : 1;

      this.form.time = undefined;
    },
    "form.adult"() {
      this.form.time = undefined;
    },
    "form.children"() {
      this.form.time = undefined;
    },
    "form.type"() {
      this.peopleSelect = Array.from(
        { length: this.orderType.maxSeats },
        (_, i) => i + 1
      );
    }
  },
  computed: {
    restaurantInfo() {
      return this.$store.state.restaurant.info;
    },
    orderType() {
      return this.timeOptionsDefault[this.form.type - 1];
    },
    formatDate: {
      get() {
        return this.$dayjs(this.form.date).format("YYYY/MM/DD ddd");
      },
      set(val) {
        this.form.date = this.$dayjs(new Date(val.substr(0, 10)));
      }
    },
    dateOptions() {
      const result = [];
      for (let i = 0; i <= 30; i++) {
        result.push(
          this.$dayjs()
            .add(i, "day")
            .format("YYYY/MM/DD ddd")
        );
      }
      return result;
    },
    isWeekend() {
      return this.form.date.day() === 0 || this.form.date.day() === 6;
    },
    timeOptions() {
      const result = this.orderType.data.map(x => ({
        label: x,
        value: this.booking[x] || 0,
        current: 0
      }));

      const _d = result;
      _d.forEach((x, i) => {
        try {
          _d[i + 1].current += x.value;
          _d[i + 2].current += x.value;
          _d[i - 1].current += x.value;
          _d[i - 2].current += x.value;
        } catch (error) {}
      });

      result.map((x, i) => {
        const data = _d.find(j => j.label === x.label);
        x.current = this.$dayjs().isAfter(
          this.$dayjs(this.form.date.format("YYYY/MM/DD ") + data.label)
        )
          ? 999
          : data.current;
      });

      if (this.form.type == 2 && !this.isWeekend) {
        for (let i = 0; i < 12; i++) {
          result.shift();
        }
      }

      return {
        label: this.orderType.label,
        options: result
      };
    }
  },
  methods: {
    async getbookingInfo(data) {
      const res = await this.$axios.get(`/booking/${data.id}`, {
        params: {
          phone: data.phone
        }
      });
      return res.data.data;
    },
    async getBooking() {
      const res = await this.$axios.get("/booking", {
        params: {
          date: this.form.date.toDate().getTime()
        }
      });
      this.booking = res.data.data;
      this.loading = false;
    },
    next() {
      if (!this.form.time) {
        this.$q.notify({
          type: "negative",
          message: `請先選擇用餐時段!`,
          timeout: 500
        });
        return;
      }

      this.$router.push({
        name: "BookingDetail",
        params: { timeForm: this.form }
      });
    }
  }
};
</script>

<style lang="scss">
.text-decoration {
  text-decoration: line-through;
}
</style>
