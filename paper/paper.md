---
title: 'Revisiting SRAmetadb.sqlite'
title_short: 'Revisiting SRAmetadb.sqlite'
tags:
  - SRA metadata
  - SQLite
  - Database
  - Biohackathon
authors:
  - name: Nishad Thalhath
    orcid: 0000-0001-9845-9714
    affiliation: 1
  - name: Nishad Thalhath
    orcid: 0000-0001-9845-9714
    affiliation: 1
affiliations:
  - name: RIKEN Center for Integrative Medical Sciences, Yokohama, Japan
    index: 1
date: 31 August 2024
cito-bibliography: references.bib
event: BH23JP
biohackathon_name: "BioHackathon Japan 2024"
biohackathon_url:   "https://2024.biohackathon.org/"
biohackathon_location: "Fukushima, Japan"
group: R4
# URL to project git repo --- should contain the actual paper.md:
git_url: https://github.com/biohackathon-japan/bh24-revisiting-srametadb.sqlite
# This is the short authors description that is used at the
# bottom of the generated paper (typically the first two authors):
authors_short: Nishad Thalhath
---

## Background

The Sequence Read Archive (SRA) is one of the largest public repositories of high-throughput sequencing data, serving as a critical resource for the global research community. It stores raw sequencing data from various studies across numerous species, offering an invaluable dataset for genomics, transcriptomics, and other fields that rely on large-scale sequence data. The metadata associated with these sequences, including information on the study's nature, experimental conditions, sample information, and data processing methods, is crucial for interpreting the raw data and conducting meaningful analyses. While SRA provides its interfaces and tools for accessing this metadata, enabling researchers to query and retrieve relevant information, the sheer scale and complexity of the data often necessitate more specialized tools. Consequently, various projects and tools have emerged to index and facilitate more efficient access to SRA metadata, underscoring its importance in bioinformatics research.

The SRAmetadb.sqlite database was initially developed as part of SRAdb project aimed at simplifying access to SRA metadata by compiling it into an offline, queryable SQLite format. This initiative sought to address the challenges posed by the vastness and complexity of the SRA data, making it more accessible for researchers who require efficient, high-speed querying capabilities without relying on constant internet access. The database became an integral resource for tools like the SRAdb R package and the pysradb Python CLI, enabling users to perform SRA metadata queries locally. Available for direct download as a single databse, SRAmetadb.sqlite provided a practical solution for handling SRA metadata, particularly in computational environments where offline access and rapid query response times are critical. Its utility extended beyond these tools, offering the potential for integration with other platforms and languages, thus enhancing its applicability across different bioinformatics workflows.

Despite its initial success, maintaining the SRAmetadb.sqlite database has become increasingly challenging due to the rapid growth in the volume and complexity of SRA metadata. Over the years, the quantity of metadata that needs to be processed has expanded significantly, with newer sequencing technologies generating vast amounts of reads and associated metadata. This exponential increase has made it increasingly difficult to manage and compile the metadata, primarily supplied as XML files, into a traditional SQL structure. The complexity of these XML files, combined with the sheer volume of data, has necessitated more sophisticated and resource-intensive processing pipelines. As a result, generating or updating the SRAmetadb.sqlite database can take several days to complete, straining computational resources and making it less feasible to maintain the database with the frequency required to keep pace with the ongoing influx of new sequencing data. Moreover, no reliable public pipeline is available for recreating this database, and performing incremental updates has become increasingly more challenging without a proper open pipeline. Along with these issues, many of the database's download links have been inactive, with the last available public version released in December 2023. These limitations underscore the urgent need for a more efficient and sustainable solution to maintain this vital resource.

## Use Cases and Rationale

Metadata analysis in bioinformatics demands versatile tools that support a wide range of use cases, from exploring datasets to conducting trend analyses. The SRAmetadb.sqlite database, while not the only solution, proves highly beneficial in addressing these needs. Below are key use cases illustrating the importance of tools like SRAmetadb.sqlite in facilitating effective metadata analysis.

1. **Dataset Discovery**:
   Enables efficient exploration and identification of datasets relevant to specific studies, aiding further analysis and curation.
2. **Related Research Identification**:
   Facilitates finding studies with similar conditions or focus, supporting collaboration and comparative analysis.
3. **Statistical and Meta-Analysis**:
   Allows for comprehensive analysis across multiple studies, enhancing data synthesis and validation.
4. **Trend and Technology Growth Analysis**:
   Provides insights into the evolution of sequencing technologies and research trends, guiding future research directions.

The SRAmetadb.sqlite database holds significant value beyond its original use in the SRAdb R package, offering potential integration with other tools and languages, such as DuckDB. Its utility can be expanded by introducing features that allow for the generation of specific subsets of SRA metadata, such as filtering datasets by species, which enhances the precision and relevance of data analysis as well as reduce the size of the resulting sqlite database for increased performance. Moreover,  SRAmetadb.sqlite can adapt to emerging use cases, including applications involving Large Language Models (LLMs) and Retrieval-Augmented Generation (RAG), ensuring its continued relevance and utility in advancing research.

## Why SQLite?

SQLite excels as a database solution in scenarios characterized by multiple read operations and a single write, making it particularly suitable for use cases like indexing SRA metadata. In this context, where the database primarily functions as a read-only system, SQLite significantly outperforms many traditional SQL options with minimal resource requirements. The efficiency of SQLite in handling read-heavy workloads, combined with its lightweight nature, ensures that it can deliver high performance even in resource-constrained environments. Unlike other SQL systems that may demand substantial computational resources, SQLite operates effectively with a fraction of the overhead, making it an ideal choice for bioinformatics tools that need to query large datasets without the burden of continuous write operations.

Moreover, SQLite’s advanced features, such as full-text search and JSON extensions, further enhance its competitiveness as a database solution. These capabilities, along with the support for vector embedding, allow SQLite to meet the demands of modern data processing and querying. The ease of maintenance and management is another critical advantage of SQLite. With no need for a server-client architecture, deployment is straightforward—requiring nothing more than copying the database file to start querying. This simplicity extends to distribution, where the database can be easily shared and used across different platforms. Additionally, SQLite is supported in all major programming languages, further reducing the complexity of integration. When properly indexed, an SQLite table can perform on par with traditional SQL databases in the same environment, offering a compelling alternative without the need for resource-intensive infrastructure.

Storing file content directly within SQLite also offers significant advantages over traditional file system storage, particularly in scenarios involving large volumes of data, such as the millions of XML files in the SRA metadata archive. In this specific use case, storing XML files on a file system not only consumes more space but also complicates retrieval. However, the workflow development demonstrated that storing these XML files as plain text within SQLite allows for more efficient storage and faster retrieval. By indexing these files with SRA IDs, the database enables rapid access to the data using standard SQLite extensions available across various programming languages. This approach simplifies the overall data management process, making it more effective and scalable. Moreover, the emergence of libraries like libSQL further extends SQLite’s capabilities, allowing it to function more like a traditional SQL server while maintaining the lightweight advantages of SQLite. This evolution supports a broader range of use cases, making SQLite an even more versatile tool for modern data processing needs.

## Design Choices and Directions

Though various highly efficient programming languages and approaches are available, JavaScript was chosen as the preferred language for the SRAmetadb.sqlite pipeline due to its evolving ecosystem and the flexibility it offers across different runtimes. While the current implementation is designed for Node.js, specifically leveraging the stability and long-term support provided by Node 20 LTS, the choice of JavaScript allows for potential future portability to other cutting-edge runtimes like Deno and Bun. This forward-looking approach ensures that the pipeline remains adaptable to new developments in the JavaScript ecosystem. Additionally, the installation and configuration processes are simplified, making the pipeline accessible to a broad range of users and environments.

Another key design choice was the deliberate avoidance of libraries that rely on native C extensions, particularly for tasks such as XML parsing. Instead, the pipeline was built using pure JavaScript libraries to ensure that it can be easily installed and executed across various environments and runtimes without the complications associated with native extensions. This approach simplifies the installation process and enhances the pipeline’s portability, making it easier to adapt to different platforms, including those that may not fully support native C extensions. By prioritizing pure JavaScript solutions, the pipeline remains flexible and accessible, aligning to create a tool that can be efficiently deployed and maintained in diverse computational settings.

The system design emphasizes efficiency in low-resource environments, allowing the pipeline to run effectively on minimal computing environment with adequate storage. This approach ensures that the database creation and processing tasks can be carried out over extended periods without incurring significant expenses. The pipeline is also designed to easily coexist within containerized environments or share resources on other servers, enabling it to operate for long durations without exhausting memory or processing resources. To further optimize resource usage, advanced features such as threading and worker threads have been integrated, minimizing the overhead and making the pipeline more suitable for environments with limited computational power.

Traditionally, many pipeline tools depend on OS-level utilities, scripts, or various external tools to function, which can impose restrictions on the environments in which they can be run. In contrast, this pipeline has been implemented entirely in JavaScript, ensuring it operates independently of any specific operating system tools or utilities. This design choice eliminates the need for additional software or dependencies beyond the JavaScript runtime, making the pipeline highly portable and flexible. As a result, the pipeline can be deployed in a wide range of environments, including containers, without concerns about compatibility or the need for supplementary tools, simplifying both deployment and maintenance.

## Methodes

One of the primary objectives of the new pipeline is to enable the effective filtering of SRA metadata, mainly based on species and, where possible, the date of study publication. This capability is crucial for researchers who need to narrow down datasets to specific criteria without processing the entire SRA archive. The pipeline is designed to generate databases that support incremental updates, allowing for additions and modifications without the need to recreate the entire database from scratch. The process begins by collecting all SRA metadata as a compressed tar.gz file, which is then streamingly transferred into an SQLite database as raw XML. This approach ensures that the large volume of data is handled efficiently while maintaining the integrity of the original metadata. In addition to this, the pipeline includes a feature to convert the SRA accession ID tab file into a properly indexed SQLite file, facilitating quick access and query operations. Finally, the pipeline processes sample IDs by extracting all samples from the dataset and indexing them alongside their corresponding taxonomy IDs. This structured indexing allows for easy filtering of samples by species, as well as the ability to combine these databases programmatically to isolate specific samples, related studies, and all associated runs and experiments. By leveraging the timestamps in the accession list, the pipeline also enables filtering based on the publication date, further refining the data selection process.

### Progress and Challenges

To implement the proposed method, a project named **SRAmetaDBB** was initiated. Most components of this pipeline are currently being implemented and tested, with several of the tested databases already published on Zenodo for evaluation purposes. However, the project remains largely a work in progress. One of the major challenges encountered is the substantial time required to test and run the pipeline due to the immense size of the SRA metadata datasets. Comprehensive testing is essential to identify edge cases and improve the tool's quality. The pipeline is designed to be highly modular, allowing for the addition of new features and enhancements as needed. The current focus is on refining the database creation process and optimizing the pipeline for addressing various usecases.

![Figure: Interface Screenshot](./images/scrrenshot.png)

The development of **SRA-Meta-DBB** is actively progressing in its GitHub repository, where ongoing improvements are made based on the results of the test cases. Despite being incomplete, the tool is already functional for creating some of the intended database stages. While still a work in progress, several key features have already been implemented, and sample output files have been published for testing and reference. These files include:

- **SRA Accessions SQLite Database**: A SQLite database containing SRA Accession IDs derived from an SRA Accessions TAB file. [Download Link](https://zenodo.org/doi/10.5281/zenodo.13501385)
- **SRAmetadb XML SQLite**: An SQLite database storing all XMLs from the SRA metadata dump file. [Download Link](https://zenodo.org/doi/10.5281/zenodo.13502652)
