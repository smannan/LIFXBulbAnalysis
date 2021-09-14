# LIFXBulbAnalysis

Analyze LIFX Bulb patterns to automate and recommend better lighting schedules to users, reducing household energy usage and providing a better
smart home experience.

Two light bulbs were purchased from Amazon and installed in the living room of a participant's home. The participants in the home continued using
the light bulbs over a period of six months.

A python client was set up to take continuous readings from the bulbs and write data to a time-series Influx database. The readings in this database
were explored and analyzed via machine learning techinques to automate and recommend better lighting schedules to the user.

Energy consumption from the bulbs was compared to overall energy usage by the home taken from a PG&E smart meter and energy usage from the thermostat,
taken from a NEST device. Users were shown a history of their energy usage over time to gain further insights into how the household used energy.

### LIFX Bulb API
https://api.developer.lifx.com/docs/list-lights - available fields generated by each light bulb

### Amazon link to purchase bulbs
https://www.amazon.com/dp/B0865TRD5L?ref=ppx_pop_mob_ap_share

### Influx DB resources
Python client: https://docs.influxdata.com/influxdb/cloud/api-guide/client-libraries/python/

Sign up: https://cloud2.influxdata.com/signup

### Preprocessing

Data queried from Influx DB includes
1. timestamp
2. brightness in percent of total lumens available
3. temperature in kelvin
4. power which is either ON or OFF

at a minute-granularity for each minute the bulb was on.

Missing Data

Measurements that contained a brightness but no temperature reading assumed the temperature to follow brightness. So if the bulb was using 100%
of its total lumens the corresponding temperature was filled in as 2.7K (the max temperature capacity for the bulb) and vice versa. If a temperature
was recorded with no brightness the lumens were inferred according to the bulb's heat.

Data was only uploaded when the bulb was on so missing timestamps were filled in and values were set to the default off - 0 lumens used and 0K
temperature reading.

We looked at the time intervals and found average time on was around 3-4 hours in the evening, which corresponded to what participant's told us
about their daily light routines.

The maximum wattage for the bulb based on the Amazon purchase was 8.5 Watts. Watts used per minute was inferred by assuming if the bulb was ON it
was using the max wattage available.

Because participants used lights on an hourly schedule brightness, temperature, and watts were aggregated over each hour, taking the average brightness
and temperature and summing the total watts per hour. Watts were converted into kWh to better compare to PG&E pricing data from the household.

The final dataframe was saved for later analysis.


