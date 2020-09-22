<template>
  <v-container>
    <v-row>
      <v-col cols="6">
        <v-select
          v-model="city"
          :items="cities"
          item-text="Name"
          label="City"
        />
      </v-col>
      <v-col cols="6">
        <v-text-field v-model="new_city" />
        <v-btn block color="success" @click="add_city">
          Add city
        </v-btn>
      </v-col>
    </v-row>
    </v-col>
    <v-row>
      <v-col cols="4">
        <v-text-field v-model="name" placeholder="Tour name" />
      </v-col>
      <v-col cols="4">
        <v-text-field v-model="description" placeholder="Tour description" />
      </v-col>
      <v-col cols="4">
        <v-text-field v-model="duration" placeholder="Est. duration (minutes)" />
      </v-col>
    </v-row>
    <v-divider />
    <v-row>
      <v-col cols="6">
        <v-text-field
          v-model="latitude"
          placeholder="Latitude"
        />
      </v-col>
      <v-col cols="6">
        <v-text-field
          v-model="longitude"
          placeholder="Longitude"
        />
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        <v-textarea
          v-model="content"
          placeholder="Content"
        />
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        <v-btn color="success" block @click="add_stop">
          Add stop
        </v-btn>
      </v-col>
    </v-row>
    <v-data-table
      :headers="stops_headers"
      :items="stops"
    />

    <v-btn color="success" @click="save_tour">
      Save tour
    </v-btn>
  </v-container>
</template>

<script>
export default {

  fetch () {
    const self = this
    self.dison = true
    this.$api.cities.get()
      .then(function (response) {
        console.log(response.data)
        self.cities.push(...response.data)
        /*
        self.city = self.cities[0]
        self.name = 'test tour name'
        self.description = 'test tour description'
        self.duration = 12
        self.stops = [{ latitude: 2.13, longitude: 2.31, content: 'heh' }]
        */
      })
      .catch(function (error) {
        console.log(error)
      })
      .then(function () {
        self.loading = false
      })
  },
  data () {
    return {
      new_city: '',
      latitude: '',
      longitude: '',
      content: '',
      city: null,
      cities: [],
      name: '',
      description: '',
      duration: '',
      stops: [],
      stops_headers: [
        {
          text: 'latitude',
          value: 'latitude'
        },
        {
          text: 'longitude',
          value: 'longitude'
        },
        {
          text: 'content',
          value: 'content'
        }
      ]
    }
  },

  methods: {
    add_stop () {
      this.stops.push({
        latitude: this.latitude,
        longitude: this.longitude,
        content: this.content
      })
      this.latitude = ''
      this.longitude = ''
      this.content = ''
    },

    add_city () {
      const self = this
      this.$api.cities.create({ city: this.new_city })
        .then(function (response) {
          self.cities = []
          self.cities.push(...response.data)
          self.city = self.cities.find(x => x.Name === self.new_city)
          self.new_city = ''
        })
        .catch(function (error) {
          console.log(error)
        })
        .then(function () {
        })
    },

    save_tour () {
      this.$api.tours.get(
        {
          tour: {
            CityId: this.city.Id,
            Name: this.name,
            Description: this.description,
            Duration: this.duration,
            Path: this.stops
          }
        })
        .then(function (response) {
          console.log(response)
        })
        .catch(function (error) {
          console.log(error)
        })
        .then(function () {
        })
    }
  }
}
</script>
