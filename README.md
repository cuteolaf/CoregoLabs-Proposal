# Corego

> This document will be part of the terms and conditions of your agreement and therefore needs to contain all the required information about the project. Don't remove any of the mandatory parts presented in bold letters or as headlines (except for the title)! Lines starting with a `>` (such as this one) should be removed. Please use markdown instead of HTML (e.g. `![](image.png)` instead of `<img>`).
> 
> See the [Grants Program Process](https://github.com/w3f/Grants-Program/#pencil-process) on how to submit a proposal.

- **Team Name:** Legal name of your team (e.g. JsonCorp)
- **Payment Address:** In the case of fiat payment, please share your bank account privately with grants@web3.foundation via your contact email (see below) and enter the date when you shared the information with us (e.g. Fiat 24.12.1971, 11:59) here. Otherwise, provide the Polkadot (for USDC & USDT) or Bitcoin payment address. Please also specify the currency. (e.g. 0x8920... (USDC))
- **[Level](https://github.com/w3f/Grants-Program/tree/master#level_slider-levels):** 1, 2 or 3

> ❗ *The combination of your GitHub account submitting the application and the payment address above will be your unique identifier during the program. Please keep them safe.*

## Project Overview 📄

### Overview

- Corego is a marketplace and a set of tools for Coretime manipulation and data tracking, with the goal of making development on Polkadot faster, easier, more flexible, and as an end result cheaper.
- Corego is a project that builds upon the ideas presented in the Agile Coretime RFC. The project will be developed under the assumption that the functionality described in the RFC will be implemented within the Polkadot/Kusama ecosystem.
  Given that the pallet containing all the logic presented in the RFC already exists, we will have the capability to undertake all the development outlined in this proposal.
* Creating proper tooling and a market for Coretime can have a significant impact on the development that takes place on Polkadot. It will enable experiments and small personal projects to be deployed to Polkadot with minimal effort and cost.

### Project Details

The project essentially consists of four parts that will create a cohesive application. In the following sections, each of them is described in great detail.

#### Coretime Abstractions

Considering that the entire project is built upon the ideas presented in the Agile Coretime RFC, there is a clear requirement to allow users to perform actions on their Coretime using Corego.


- **Regions Dashboard** will serve as a central hub for users, allowing them to browse all of the regions they own. The regions will be presented as UI card components containing all relevant metadata and actions that a user can perform on their region.
  TODO: IMAGE

- **Partitioning** UI will offer users a convenient method for splitting their region. The UI will visually represent the region as a timeline, highlighting all potential pivotal points available for partitioning.

  <p align="center">
    <img width="800" src="https://i.postimg.cc/zB7y0tkk/parititoning-v2.png"/>
  </p>

- **Interlacing** UI will also allow users to easily interlace the region they own. The user will be able to specify the region core mask as a fraction. The UI will visually show a block colored with a distinct color based on the fraction. TODO: Provide a better image
  <p align="center">
    <img width="300" src="https://i.postimg.cc/brdwLzrz/image.png"/>
  </p>

- **Naming Regions & Tasks**: Users will have the option to assign names to their regions and tasks, making it simple to differentiate them.

- **Assignment** UI will make it straightforward for users to allocate a task to their owned regions. The UI will display a dropdown menu containing all the tasks that the user has published to the relay chain, simplifying the selection process. TODO: better image
  <p align="center">
    <img width="400" src="https://i.postimg.cc/ncjLDcN2/image-2.png"/>
  </p>

- **Transfer** UI will offer a straightforward modal for users to perform transfers. TODO: better image
  <p align="center">
    <img width="300" src="https://i.postimg.cc/k59MzNGY/transfer.png"/>
  </p>

#### Secondary Market

Coretime is a resource that goes to waste if not utilized during its intended time. When you purchase a core for a month, it means you can use a maximum of one core at any given moment during that specific time period. If the core is not utilized, the coretime is essentially squandered.

After buying coretime during the bulk period, one may realize that the purchased coretime is either too much or too little for the intended use. The secondary market assists these individuals and teams in rectifying their mistakes by enabling them to potentially sell or buy more coretime.

Coretime can be partitioned and interlaced meaning most of the coretime on sale will hardly be the same. For this reason we are going to utilise the order book model.

The seller will need to specify the price for the entire region, and based on that, the contract will calculate the price of one bit, which is equivalent to 1/80th of the price of the entire region.

This means that the total value of the coretime owned by the seller will go down every time the smallest unit of computation is not used. From here, we can come to the conclusion that the price of the region that is being on sale is dynamic.

**The steps of selling and buying coretime:**

1.  A user decides to sell one of their regions.
	-   The user defines the price that they intend to sell the region for.
	-   The contract calculates the price per one bit. 
2.  Another user decides to buy some coretime.
	-   They are browsing the market, and they have decided they want to buy a specific region.   
	-   The user will have to pay the price for the region; however, this won't be the price of the entire region. The price will be defined by remaining_smallest_units * price_per_unit.
3.  The user pays the price, the seller receives the paid amount, and the buyer receives the region.

In case a user doesn't want to buy the entire region but only a part of it, the buyer will need to specify which parts of the region they want and provide the steps to actually create a region that has the properties they desire.
The contract will be able to follow the steps the buyer provides and create a region that the buyer wants to buy. The contract will also calculate the amount that needs to be paid for that and charge the buyer the appropriate amount. After all these steps, the seller will receive their tokens, and the buyer will obtain their desired coretime.
We refer to this feature  **Region Derivation**. It will enable buyers to have more options when purchasing coretime, making it easier to meet their specific needs.

TODO: Should we provide an example here from the market doc?

**Defining the price of coretime**
The price of coretime will be highly influenced by supply and demand. Since we are constructing a market with an NFT order book model, users will have the authority to establish the price of the coretime they intend to sell.

Depending on whether the seller owns an entire core, only partitioned parts, or has it interlaced, the selling price of the coretime will be affected.

As mentioned earlier, the pricing of a region will be determined at a bit level. This approach proves particularly useful because it allows us to establish a pricing structure that decreases when coretime is wasted.

In situations where the buyer's instructions involve partitioning the region and performing interlacing on the partitioned region, the price will be determined based on the bit price of the resulting partitioned region.

This approach allows us to easily calculate the price of the region the buyer intends to purchase, even in situations where the buyer requires multiple instructions to be executed on the region.

**Formula to calculate the price when partitioning:**
`price = bit_price * pivot_defined_as_bit`

**Formula to calculate the price when interlacing:**
`price = bit_price * new_region_length_in_bits`

**Region Derivation**
As demonstrated in the previous example, buyers have the option to acquire only a portion of a region. This is achieved by requesting a set of instructions from the buyer to generate the desired region. However, manually specifying these instructions can be a challenging task for users.

The approach we aim to adopt here is to enable users to describe the desired characteristics of the region they require. Using all the provided input, the frontend will determine whether the specified region can be generated from all regions listed on the market. If a match is found, the user will be presented with a price for their region. Future iterations of this feature may include Natural Language Processing (NLP) to describe the region’s characteristics.

**Market Architecture**
The coretime marketplace can be implemented in four different ways, which include:

1.  Ink! smart contract
2.  Solidity smart contract
3.  Actor (Not yet implemented in Polkadot/Kusama)
4.  Parachain

From these options, we've selected the Ink! smart contract as our initial approach. As the project evolves, we anticipate transitioning to either an actor or a separate parachain to access greater possibilities to improve our services.
We possess a much more comprehensive overview of all these options, which we chose not to include here to avoid making this proposal too lengthy. However, we can provide it if there is interest.

**Implementation Requirements**
We came up with an implementation design that makes it possible to develop the market as an ink! smart contract located on a contracts parachain in the Polkadot/Kusama ecosystem.
Our solution has very minimal and reasonable assumptions that are required to make this possible. 

Our sole assumption is that the concepts outlined in the Agile Coretime RFC are implemented in Polkadot/Kusama. We do not have any specific assumptions concerning the XCM configuration on the Coretime parachain to make this work. We only require that the Coretime parachain allows basic reserve transfers.

The only feature with a slightly greater assumption is region derivation. To enable region derivation, we need the Coretime parachain and the contracts parachain to support the `Transact` XCM instruction. 
This is because regions can only be partitioned or interlaced on the Coretime parachain. Consequently, during region derivation, the region must be transferred to the Coretime parachain, where all instructions are executed (this is where the `Transact` instruction is required). Subsequently, the new regions are transferred back to the contracts parachain, where one of the regions is sent to the buyer, while the remaining regions are listed again on the market.

**Region NFT Contract**
To create a marketplace on a contracts parachain, we'll need an NFT region contract. We'll use the openbrush library to simplify development, as it will only require a few adjustments.

#### Data Dashboard

Corego's Data Dashboard is a central hub for users, offering essential insights and real-time market data. With a user-friendly interface, it provides context, data from the Coretime chain, market information, and other useful insights. This empowers users to make informed decisions, whether they are buying or selling coretime or performing other operations.

We have divided the data dashboard into four sections.

**1. Constants**
Displaying configuration constants to the user is be highly beneficial, giving them a more comprehensive macro understanding of the system.

For instance, by tracking the volume of coretime channeled into the Instantaneous Coretime Pool, we can gauge the prevailing demand for coretime. Delving deeper, we could measure pool payouts and determine a ratio comparing the volume of coretime sold in the pool to the volume deposited. Such metrics, among others, would provide invaluable information.

Data shown in the constants section:
-   Interlude Period Length: Time during which the bulk sale is not active.   
-   Leadin Period Length: Time during which the sale price varies.
-   Region length: The length of the regions available for purchase during the upcoming bulk sale.
-   Cores Limit: The maximum number of cores that can be sold.
-   Renewal Price Increase: The maximum price increase of a renewable region.

**2. Coretime Chain**
While we don't directly oversee the operations of the coretime chain, indexing data from the primary market can offer significant insights.

Data
-   Reserved Cores: Cores utilized by system parachains.
-   Renewed Cores: Cores renewed by non-system parachains.
-   Leases: Number of leases.
-   Current Bulk Sale Period: Ongoing period of bulk sale.
-   Bulk Sale Offerings: Number of cores offered in the bulk sale.
-   Bulk Sale Transactions: Number of cores sold.
-   Unassigned Regions: Total number of regions without assignments.
-   Assigned Regions: Total number of regions with assignments.
-   Region Status: Number of finalized and provisional regions.
-   Leadin Period Price Trend: Continuous decrease in bulk coretime price.
-   Post-Leadin Coretime Price: Price after the leadin period.
-   Core Utilization: Percentage of utilized cores (with a graph for time-based visualization).
-   Region Utilization: Percentage of utilized regions (with a graph for time-based visualization).
-   Instantaneous Pool Data
-   Contributor Input: Circle graph showcasing contributor-provided and system-provided bits, akin to the income graph on the dotreasury website.
-   Coretime Purchases: Volume purchased during a specific time frame.
-   Pending Payouts: Unclaimed payouts, reminiscent of the 'to be awarded' data on the dotreasury website.

**3. Market**
As the secondary market expands with increasing user transitions, it becomes crucial to index pertinent data.

For instance, by tracking daily, weekly, or other periodic purchases and sales, users can gain a clearer understanding of coretime's price trends. Moreover, real-time indexing of coretime's price can offer immediate insights. Such data indexing could pave the way for aggregators to present users with diverse liquidity options across multiple coretime markets akin to the 1inch protocol.

Data
-   Regions on sale (for a specified period)
-   Monthly Regions Sold (with future potentials for a ratio - (sold/on sale)).
-   Purchases: Number for a specified period (day, week, etc.).
-   Market Dominance: Share of secondary market.
-   Trading Volume: For a specified period in USD and DOT.
-   Total Transactions: Made between unique active wallets and contracts.
-   Total Users: Registered users with at least one market transaction.
-   Monthly Active Users: Registered users with at least one monthly transaction.
-   Average Price Per Timeslice: With a graph depicting data over time in USD and DOT.
-   Regions flowing in and out of the parachain where Corego is deployed.

**4. Tooling**

As outlined earlier, users have an array of tools at their disposal to modify their regions. Compiling data on these modifications, such as the number of interlaced or partitioned regions at any given moment, can offer robust insights into market activity and specialization.

Data:
*The number of occurrences during a specified time period:*
1.  Partitioning
2.  Interlacing
3.  Assignments
4.  Transfers

#### Developer Console

The developer console is a pivotal tool, aiding teams in navigating the coretime market and managing their owned regions. Corego stands out as the only cloud management platform tailored exclusively for the Polkadot ecosystem. It mirrors the capabilities of centralized cloud computing management platforms; it will provide valuable data and reporting, inform users via alerts, and eventually offer recommendations based on machine learning models to improve resource allocation. Ultimately, the developer console’s key focus is to optimize teams computing spendings.

We have split the developer console into two sections: Coretime management and alerts.

**Coretime Managment**

This section is further subdivided into three subsequent sections, which are described in detail below.

**1. Workload**
Observing the workload for each region will help users to make well-informed decisions on their regions.

TODO: add design image here
Visual description:

Similarly to how we showcase the regions in the base design we will list all of the regions that are assigned to a specific task. However, we won’t show all the data here that is part of the base design since it isn’t really relevant.

We will showcase the following metadata on each card item:
1.  Region name
2.  Task name
3.  Assigned at (block number or date)
4.  Finality

Additional visuals:

1.  A graph showcasing the utilization of coretime by the given task. The graph should contain the maximum coretime that can be utilized by the given region and the actual consumption.

2.  The timeline of the region. This will show all the operations that were done on the region to achieve its current form.

3.  The UI will indicate the upcoming task within this region if there is one scheduled. It will show the task name and the start time.

  <p align="center">
    <img width="600" src="https://gcdnb.pbrd.co/images/l5W9tE6szrNf.png?o=1"/>
  </p>

**2. Tasks**

The list of tasks the user published to the relay chain. We anticipate this to help the user to keep track of all their tasks and upload any new tasks before assignment to a region. 

The UI will list all of the tasks that the user uploaded to the relay chain. It will also contain the following set of options:
1.  Upload task
2.  Rename task

**3. Costs**

The price the user paid per region and the total cost of all the regions will provide users a view of Polkadot spending at a glance.
It is useful to be aware of the financial cost of the regions to make future optimizations.

TODO: use image instead
*Visual description:*
Similarly to how we showcase the regions in the regions dashboard design we will list all of the regions. However, we will only show the cost related data here including:
-   Price paid for the entire region
-   Price of the region defined per timeslice
-   The current average price per timeslice for internal comparisons

**Alerts**

We will implement these alerts using web3alert until we move onto our own infrastructure for more advanced alerts.

**1. Region Sold**
When a user's listed region is sold, they will be notified. This ensures that the user is promptly informed of the successful sale of their region.

**2. Task Started**
When a scheduled task becomes the current workload on a user's region, the user will be alerted. This is crucial to update users on the new tasks running in their region.

**3. ICP Payout Available**
Users will be notified when a payout is available for their coretime contribution to the instantaneous pool, which serves as a reminder to claim their due payouts.

**4. Available Cores**
Users will be informed about the number of cores available for the next bulk sale. This alert aims to help users prepare for the upcoming sale, especially if there are changes in core limits.

**Integrations**

Being able to export consumption and utilization reports and share general analysis to the most popular communication networks (Telegram , Discord and Slack) provides teams the flexibility needed to collaborate effectively and perform tasks such as accounting.

### Ecosystem Fit

Corego stands out as a pivotal player within the ecosystem, strategically built atop the coretime-chain. Corego is committed to optimizing resource allocations, and to do so, we'll leverage the chain's unique functionalities to offer advanced abstractions, a coretime-focused indexer, a secondary market, and a developer console. 
This multifaceted approach caters to teams across the Polkadot and Kusama ecosystems. It provides them with the tools to seamlessly interact with the coretime-chain, gain deep insights into coretime markets, trade execution cores effectively and react based on usage details. 
As integrating the novel coretime concept into Polkadot unfolds, we remain vigilant, anticipating potential competitors in this emerging space.

## Team :busts_in_silhouette:

### Team members

- Julian Saks
- Sergej Sakac

### Contact

- **Contact Name:** Julian Saks, Sergej Sakac
- **Contact Email:** juliansaks@gmail.com, sakacszergej@gmail.com
- **Website:** TODO

### Legal Structure

- **Registered Address:** Kanalska 7 Novi Sad Serbia
- **Registered Legal Entity:** MASTER UNION LLC.

### Team's experience

Julian is the President of Texas Blockchain and a third-year student at the University of Texas at Austin. He has scaled Texas Blockchain to one of the largest university blockchain clubs in the United States and the world. Julian is also a recent Founder alumni of the Polkadot Blockchain Academy (PBA) held in Berkeley.

Sergej is a member of the Polkadot Fellowship. He has been an external core contributor on substrate and polkadot for more than a year now. Sergej is also a recent Engineering alumni of the Polkadot Blockchain Academy (PBA) held in Berkeley.

### Team Code Repos

Github organization:
https://github.com/CoregoLabs

Github profiles of team mebers:
- https://github.com/Szegoo
- https://github.com/kingjulio8238

### Team LinkedIn Profiles (if available)

- linkedin.com/in/juliansaks/
- linkedin.com/in/sergej-sakac-334a47252

## Development Roadmap 🔩

### [Overview](https://github.com/w3f/Grants-Program/blob/master/applications/application-template.md#overview-1)

-   **Total Estimated Duration:**  TBD
-   **FTE:**  3.5 (3 + 0.5 designer)
-   **Total Costs:**  TBD

### Milestone 1 - Coretime Abstractions

-   **Estimated duration:**  1 month
-   **FTE:**  3.5
-   **Costs:**  TBD

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| **0a.** | License | MIT (?) |
| **0b.** | Documentation | We will create documentation that thoroughly explains all aspects of the UI. Our goal is to design the UI to be as intuitive as possible, so users require minimal familiarization with the project. |
| **0c.** | Testing and Testing Guide | All interactions with the Coretime parachain will undergo comprehensive testing to guarantee a seamless experience for users when using the Corego UI. We will be running a local zombienet network to simulate the existance of a Coretime parachain. |
| **0d.** | Docker | We will provide a Dockerfile that will set up the and run the Corego Coretime abstractions UI. |
| 0e. | Article | TODO |
| 1. | Design finalization | We will complete the design for the Coretime Abstraction UI, and based on that, we will proceed to develop the frontend code. |
| 2. | Mock Coretime Parachain runtime  | We will establish a parachain dedicated to testing the Coretime abstractions. Essentially, this involves creating a parachain that implements the `broker pallet`. We will use this runtime in our zombienet network. This will be used in the upcoming milestones as well. |
| 3. | Coretime Abstraction UI | We will implement all the sections and components described in the *Coretime Abstractions* section above. To summarize, this will consist of the following components: region dashboard, partitioning UI, interlacing UI, naming regions & tasks components, assignment UI and transfer UI |


### Milestone 2 - Cross Chain Regions

-   **Estimated duration:**  1 month
-   **FTE:**  3.5
-   **Costs:**  TBD

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| **0a.** | License | MIT (?) |
| **0b.** | Documentation | The ink! smart contract will be well written and documented. We will also create documentation that thoroughly explains all aspects of the UI. Our goal is to design the UI to be as intuitive as possible, so users require minimal familiarization with the project. |
| **0c.** | Testing and Testing Guide |The ink! smart contract will undergo thorough testing, including e2e testing with a simulated zombienet network, to ensure maximum correctness. All UI interactions will undergo comprehensive testing to guarantee a seamless experience for users when using the Corego UI. |
| **0d.** | Docker | We will provide Dockerfiles for the ink! smart contracts that will set up the environment and execute the contract tests. Additionally, we will offer a Dockerfile that will configure and run the Corego UI. |
| 0e. | Article | TODO |
| 1. | Region NFT contract | As described in the previous sections, we will create an ink! smart contract that will be representing regions on the contracts parachain where we choose to deploy Corego. |
| 2. | Finalize cross-chain UI Design | We will finalize the design for the cross-chain region transfer UI. Following this, we will proceed to develop the frontend code. |
| 3. | Cross-chain Transfer UI | We will create the UI for transferring the region NFTs from the Coretime parachain to the contracts parachain and vice versa. |


### Milestone 3 - Coretime Market

-   **Estimated duration:**  2 months
-   **FTE:**  3.5
-   **Costs:**  TBD

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| **0a.** | License | MIT (?) |
| **0b.** | Documentation | The ink! smart contracts will be well written and documented. We will also create documentation that thoroughly explains all aspects of the UI. Our goal is to design the UI to be as intuitive as possible, so users require minimal familiarization with the project. |
| **0c.** | Testing and Testing Guide | The ink! smart contracts will undergo thorough testing, including ink! integration and end-to-end tests, to ensure maximum correctness. All UI interactions will undergo comprehensive testing to guarantee a seamless experience for users when using the Corego UI. |
| **0d.** | Docker | We will provide Dockerfiles for the ink! smart contracts that will set up the environment and execute the contract tests. Additionally, we will offer a Dockerfile that will configure and run the Corego UI. |
| 0e. | Article | TODO |
| 1. | Coretime Market contract | We will develop the Coretime market as an ink! smart contract, as described above in the *Secondary Market* section. |
| 2. | Finalize market UI designs | We will finalize the design for the Coretime market UI. Following this, we will proceed to develop the frontend code. |
| 3. | Coretime Market UI | We will create the UI for interacting with all the functionality exposed by the Coretime market contract. The UI will also offer an intuitive design that allows the buyer to describe their desired region. |

### Milestone 4 - Data Dashboard

-   **Estimated duration:**  1 month
-   **FTE:**  3.5
-   **Costs:**  TBD

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| **0a.** | License | MIT (?) |
| **0b.** | Documentation | We will create documentation that thoroughly explains all aspects of the UI. Our goal is to design the UI to be as intuitive as possible, so users require minimal familiarization with the project. |
| **0c.** | Testing and Testing Guide | The data dashboard doesn't contain any critical logic; nevertheless, we will ensure that all the data displayed on the dashboard is accurate. |
| **0d.** | Docker | We will provide Dockerfiles for the ink! smart contracts that that will configure and run the Corego UI. |
| 0e. | Article | TODO |
| 1. | Finalize data dashboard UI designs | We will finalize the design for the data dashboard UI. Following this, we will proceed to develop the frontend code. |
| 2. | Data Dashboard UI | We will create the data dashboard UI, which will include all the sections described in the project details section of the proposal. |

### Milestone 5 - Developer Console

-   **Estimated duration:**  1.5 month
-   **FTE:**  3.5
-   **Costs:**  TBD

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| **0a.** | License | MIT (?) |
| **0b.** | Documentation | We will create documentation that thoroughly explains all aspects of the UI. Our goal is to design the UI to be as intuitive as possible, so users require minimal familiarization with the project. |
| **0c.** | Testing and Testing Guide | All UI interactions will undergo comprehensive testing to guarantee a seamless experience for users when using the Corego UI. We will additionally ensure that the alerts are working correctly. |
| **0d.** | Docker | We will provide Dockerfiles for the ink! smart contracts that that will configure and run the Corego UI. |
| 0e. | Article | TODO |
| 1. | Developer Console design | We will finalize the design for the developer console UI. Following this, we will proceed to develop the frontend code. |
| 2. | Coretime Managment | We will implement all the components mentioned in the *Coretime management* section above. |
| 3. | Coretime Managment | We will implement all the alerts mentioned in the "Alerts" section above using the web3alert SDK. |

## Future Plans

By completing all the milestones, we will have all the components of Corego in place. Our plan is for future work to be built upon these components.

The following sections contain the plans and ideas we currently have, and it is highly likely that this list will expand as we work on the milestones outlined in this document.

#### Coretime Market

- Transitioning from an Ink! contract to a parachain (or even an actor) will provide us with greater flexibility over XCM, improved performance, and the possibility to more easily integrate with other pallets.
- We intend to incorporate the capability to describe a region using NLP. This approach will allow users to easily describe the region they are interested in, and the frontend will subsequently present them with all the available options to choose from, along with their respective prices.

#### Data Dashboard

With the emergence of shared builders, which aggregate and batch work items into work packages for attestations, a growing value add exists to offer teams structured, indexed data on these builders. Such data can help teams discern which cores are currently in use, identify cores nearing their capacity, understand the average allocation required for builders to collect and refine, and gain insights into other vital metrics.

Data of the new section:
1.  Active builders on last relay chain block
2.  Volume of work items processed by the shared builder group on the last relay chain block
3.  Categories of work classes processed (Actor, Parachain…)  
4.  Analysis of builder's coretime consumption.
5.  Payout metrics for builders (once builders are deployed on Polkadot/Kusama)

#### Developer Console

**Advisor**

A machine learning model akin to the Azure advisor recommending the assignment size and associated costs for a specific task. Providing users with these recommendations will ensure that they don’t buy unnecessary coretime. Our model will be trained from the first usage and recommends assignments for tasks based on that usage information for optimized costs. As the model receives more data from one bulk period to another, it will improve to offer more accurate recommendations.

As the advisor model evolves and gathers more data, it will adeptly discern the computational demands of various tasks. This refined understanding will empower it to provide precise coretime recommendations from the very first period, leading to substantial savings for teams.

**Identity and Access Management**

Including the functionality to add multiple team members to the console promotes team collaboration. Alongside having multiple identities, we will include permissions to avoid coretime misuse. The integration of the multisig pallet is a clear solution to achieve such and will make sense once Corego has transitioned to a specialized chain with more flexibility to offer such features.

**Team KPIs**

Offering the ability for teams to set cost and utilization KPIs will allow them to set goals to Improve performance and perform analysis to rectify mistakes.

An example of a KPI for tracking core utilization:
```
UtilizationIndex = capacity - |capacity * 0.8 - consumption|
```

This KPI assumes that the optimal coretime usage is 80% of the purchased coretime. Any deviation from this will result in a reduced value for the utilization index.

**Gamification**

Offering rewards for optimizations by integrating an ‘optimization leaderboard’ will expose bad practices and in doing so encourage teams to be more computationally efficient.

To achieve this we will use a specific KPI: (coretime utilized / coretime bought) to signify a team's computational efficiency. The leaderboard will list users in descending order based on this KPI.

**Additional Alerts**

We will implement these alerts using our own infrastructure which will allow us to export more data and integrate with more communication networks (Matrix, Whatsapp, Email). This commitment to having infrastructure assigned to alerts will specifically benefit us as we anticipate most notification services won’t support our chain early on.

- **Expiring Region Warning**
	Warning the user whenever one of his regions is close to expiry will remind him to potentially buy a new region for future assignments.

	We look to integrate this alert by creating an unique event that emits when the region’s parameter ‘when’ exceeds ‘last_until’ and emit an event to reflect this. We will listen for this event and alert the user when it is emitted.

- **Wastage Reporting**
	Notifying the user in case of utilizing a very small portion of the owned coretime may influence the user to reconsider their usage optimizations and potentially decide to sell part of their region on the market.

	We will gather averages of the user’s consumption over time and once data is sufficient we will detect scenarios where the consumption is a lot less than the all-time average. We will listen for this event and alert the user when it is emitted.

- **High Usage Reporting**
	Alerting the user that a lot of his coretime is being utilized may influence him to buy more coretime on the market or stop certain tasks.

	In the same way as reporting wastage, we will gather averages of the user’s consumption over time and we’ll detect scenarios where the consumption is more than the all-time average and emit an event to reflect this. We will listen for this event and alert the user when it is emitted.
