# Carbon Footprint Estimation

## 1. Scope of Measurement

The carbon emissions resulting from the consumption of services on the cloud can be categorized into two types:

- **Electricity consumption**: This is the scope 2 of the cloud provider,
which corresponds to the carbon equivalent emitted by the production of electricity (coal-fired power plants, solar power, etc.), consumed by the hardware (server, air conditioning, etc.) to provide the requested service.
- **Material value chain**: This is the scope 3 of the cloud provider,
which corresponds to the carbon equivalent emitted by the production and transportation of the hardware used to provide the requested service.

IroCO2 currently only takes into account electricity consumption to estimate the carbon footprint of your resources on the cloud.
The inclusion of the material value chain will be integrated when data sources,
regarding the physical hardware used by a cloud resource and its lifecycle within data centers, become more available.

## 2. Consumption Parameters

To estimate the electricity consumption of a service consumed on the cloud, several criteria must be taken into account:

### 2.1. CPU

The allocation of CPU on the cloud is done through the notion of vCPU.
Given that one physical core of a CPU can be considered equal to one vCPU (in the virtualization of hardware resources), we can establish a relationship between physical and virtual resources.

It is then necessary to take into account the concept of TDP (Thermal Design Power), which, according to CPU manufacturers, can be approximated to the maximum power consumption of a processor (ex. Intel).
We can therefore conclude that if a CPU has a TDP of 100W (at 100% of its capacity), and it is used at 50% of its capacity, then it will have a power consumption of 50W. To simplify the calculation in the context of the Estimator, we assume that the CPU being studied is constantly used at 50% of its capacity. The aims is to improve the methodology to take into account the CPU usage in the future.

It is not possible to predict with certainty which precise CPU model will be allocated by the cloud provider to your resource. However, there is a way to distinguish between CPUs before deployment in order to eco-design your cloud infrastructure.

To do this, we base ourselves on the type of CPU architecture, which significantly affects the average TDP. For example, ARM processors will have a much lower average TDP than x86 processors (ex. Graviton in AWS can save up to 60% of energy).

By taking all these elements into account, we can estimate the electricity consumption of a set of vCPUs allocated to a resource on the cloud with the following formula:

$$
\text{Electricity consumption} = 0.5
\times \text{Number of CPUs}
\times \frac{\text{TDP of the CPU architecture}}{\text{Number of physical cores}}
\times \text{Usage time}
$$

### 2.2. Memory

We use the power consumption constants from the [Etsy Cloud Jewels study](https://www.etsy.com/codeascraft/cloud-jewels-estimating-kwh-in-the-cloud).

### 2.3. Storage

We use the power consumption constants from the [Etsy Cloud Jewels study](https://www.etsy.com/codeascraft/cloud-jewels-estimating-kwh-in-the-cloud).

### 2.4. GPU

To estimate the electricity consumption of a GPU,
we base ourselves on the same principles as the CPU calculation,
except that we take into account the entire GPU,
there is no notion of vGPU in our case study.

## 3. Energy Mix

The energy mix of a country or region represents the distribution of different types of energy production in the area.
This distribution makes it possible to determine the carbon cost of the energy consumed in that location, expressed in gCO2eq/kWh.

It is with this data that we are able to estimate the carbon footprint of the energy consumed for your activities on the cloud.
For example, if your resources deployed in France consume 50 kWh and given the carbon cost of French energy of approximately 30 gCO2eq/kWh,
then the carbon footprint of using these resources will be 1.5 kgCO2eq.

## 4. Limitations

Here is a list of general parameters that we do not currently take into account:

* Network traffic electricity consumption
* PUE of AWS data centers (not provided)
* Non-exhaustive replication of resources
* No distinction between different Graviton processor models
* No distinction between different types of storage (HDD, SSD, etc.)

You can find the specific limitations for each cloud service compatible with IroCO2.

## 5. Sources

* Cloud Jewels : [Estimating kwh in the cloud](https://www.etsy.com/codeascraft/cloud-jewels-estimating-kwh-in-the-cloud)
* Cloud Carbon Footprint : [Methodology](https://www.cloudcarbonfootprint.org/docs/methodology/)
* Microsoft : [How can I calculate co2eq emissions for my Azure VM?](https://devblogs.microsoft.com/sustainable-software/how-can-i-calculate-co2eq-emissions-for-my-azure-vm/)
* Intel : [Processor specifications and compatibility](https://www.intel.com/content/www/us/en/support/articles/000055611/processors.html)
* [AWS Documentation (physical specification of services, replication, ...)](https://docs.aws.amazon.com/)
