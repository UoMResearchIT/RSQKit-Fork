# Capilytics: Developing an AI-assisted capillaroscopy system to support earlier diagnosis of systemic sclerosis

**Draft 0 — Research Software Story**

## The Problem

Systemic sclerosis (SSc) is a painful and disabling condition associated with high mortality. Early diagnosis is important, but identifying the disease can be difficult. Nailfold capillaroscopy has been recognised for more than 20 years as an important tool for early diagnosis, yet it is not generally available in most UK rheumatology clinics. This creates missed opportunities for early diagnosis, potentially poorer patient outcomes and avoidable costs for the NHS.

Many patients with SSc initially present to their GP with Raynaud's phenomenon. However, Raynaud's is not specific to SSc and is also experienced benignly by around 5% of the population. Capillaroscopy can reveal microvascular abnormalities associated with SSc and help identify patients who need further investigation. The NIHR project described the challenge of identifying approximately 1,200 people with SSc from the 16,750 people with Raynaud's referred annually in the UK. One reason capillaroscopy is not more widely available is that acquiring suitable images and interpreting them both require specialist expertise.

Capilytics aims to make capillaroscopy more accessible by supporting clinicians who may have little previous experience of the technique. Acquiring nailfold images is not straightforward without training from an experienced practitioner. Furthermore, deciding whether borderline images warrant further investigation and monitoring for possible SSc can be difficult, even for experienced observers. Capilytics aims to augment rather than replace clinical expertise, providing numerical abnormality scores and visual explanations to help clinicians make more informed decisions.

## The User Community

Capilytics has been developed through a collaboration that originally began at least a decade ago between researchers in the Imaging Sciences department at the University of Manchester and clinicians at Salford Royal Hospital. The collaboration has expanded during the current NIHR-funded project to include additional Manchester researchers, research software engineers (RSEs), research statisticians, clinical trial specialists, capillaroscopy experts from elsewhere in the UK and patient advocates.

The intended users include rheumatologists, nurses and clinical technicians. Importantly, the system is intended to support people who may not have extensive previous experience of capillaroscopy. Patients are indirect beneficiaries, as wider availability of capillaroscopy could improve access to assessment. The NHS may also benefit if more clinical staff are able to offer the technique. Patient advocates contribute through workshops and questionnaires, providing feedback on patient experience that helps inform subsequent user-interface development. The extent to which the resulting clinical data will support research beyond the current project remains to be established.

## Technical Aspects

Capilytics comprises a Windows desktop application for image acquisition and a cloud-based service for image processing, analysis and report generation. The desktop application communicates with the cloud service by transferring images and associated information, while clinical reports are returned to the desktop application. Python is the principal programming language throughout the system. The desktop application uses PyQt, while FastAPI provides the interface to the cloud service. PyTorch is used for the AI components.

The acquisition and image-analysis models are separate models, although they share some layers based on a ResNet-type neural network architecture. During image acquisition, the software provides real-time visual feedback to guide the clinician on where to position and how to move the microscope. Optional audio feedback is also available, particularly when a user is first learning to use the software. An AI-based image-quality metric determines when images are suitable for collection. It considers factors such as capillary visibility, the vertical positioning of capillaries in the image and the horizontal alignment of the nailfold bed line. The interface displays a quality score ranging from red, through yellow, to green, alongside directional and rotational guidance to help the user improve image quality.

Processing speed is the main technical constraint. The computers running the desktop application need sufficiently capable GPUs to support real-time operation. The amount of image data is not considered a major concern: approximately four images per nailfold are required, with images acquired from all fingers except the thumbs. This amounts to approximately 32 images per patient in a typical session. The number of capillaries and the morphological anomalies detected contribute to the numerical abnormality score.

An important architectural consideration is the reliability of internet connections at clinical centres. Image acquisition does not depend on an internet connection being available. If connectivity is unavailable, images and associated information are stored in a local database and synchronised with the cloud service when the acquisition software is open and a connection becomes available. Where connectivity is reliable, images are sent to the cloud after the acquisition session. Processing and report generation take approximately ten minutes, allowing clinicians and patients to receive feedback within this timeframe when the connection is reliable.

## Libraries and Systems

The system is primarily developed in Python, using PyQt for the desktop application, FastAPI for the cloud-service interface and PyTorch for the AI models. The microscope used is a Dino-Lite, which has its own C++ interface. This is the only C++ component in the system. Python interacts with this interface through pybind11, principally to retrieve microscope information other than the image itself, such as the magnification value.

The cloud-based software is hosted on a virtual machine at the University of Manchester. PostgreSQL is used for the databases, with SQLAlchemy and Alembic supporting database interaction and management. Together, these technologies provide the infrastructure for local image storage, synchronisation and cloud-based processing.

## Software Practices

The software is maintained across separate GitHub repositories covering the acquisition application, API, processing, reporting, and the underlying metrics and models. The back-end components are also brought together through a hub repository that uses Git submodules to incorporate the API, processing, reporting and web user-interface components.

Each repository has its own unit and integration tests, using pytest. The acquisition application also uses pytest-qt to simulate user interactions. Developers work on branches, with pull requests into the development branch requiring review by another developer and successful completion of the relevant tests. Code documentation is checked during this review process. Releases are made by merging the development branch into the main branch, with two reviews required before the merge.

Issues and bugs are managed through GitHub and GitHub Projects where required. Deployment has been somewhat automated, although manual deployment remains possible given the relatively small number of clinical trial centres. The deployment procedures are documented, providing a basis for adapting the process if the software is used more widely. As the system is intended for use as a medical device, development procedures also need to meet the requirements of ISO 62304, as part of the project's work towards MHRA registration.

## Developer Community

The main driver of Capilytics development is a researcher in Imaging Sciences who has worked on the software for several years and holds much of the core technical knowledge. During the development phase of the current NIHR grant, four RSEs contributed to the different aspects of the system over approximately two years. The software is now in its clinical trial phase, with the researcher maintaining it and receiving assistance from RSEs and members of the Research Platforms team when needed.

Two professors in Imaging Sciences provide technical oversight of the researcher, bringing broader expertise in machine learning and medical imaging. Two project managers coordinate the technical work and clinical trial activities and have direct influence over project decisions and knowledge sharing. The RSEs came from the central ResearchIT team at the University of Manchester, with their own project manager overseeing progress and resource allocation. The RSE team initially comprised two people, expanded to three when other commitments affected availability, and subsequently reduced to one for an initial maintenance period after development was complete.

The collaboration between the researcher and RSEs was particularly important because the scale of the work exceeded what one person could reasonably deliver alone. During development, the team established collaborative ways of working informed by the agile processes that the central Manchester RSE team aspires to use. This collaboration was highly valued by the researcher and wider technical team.

## Tools

Visual Studio Code was generally used as the development environment. GitHub provides version control, code review, testing workflows and issue management, while GitHub Projects is used to manage adding new features, dealing with issues and fixing bugs.

Clinical data management is handled by the project managers and clinical trial leads. They developed a form of quality management system (QMS) for this purpose. At the current clinical trial stage, integration with NHS systems has not been necessary. The local databases associated with the desktop application and the main database on the University of Manchester virtual machine have been sufficient for the software's data-management requirements.

## FAIR & Open

- **Findable:** Information about Capilytics is available through the [project website](https://capilytics.manchester.ac.uk/), the [CapilyticsManchester GitHub organisation](https://github.com/CapilyticsManchester) and the [public NIHR funding award record](https://www.fundingawards.nihr.ac.uk/award/NIHR204551). The current visibility and access permissions of the GitHub repositories have not been confirmed.

- **Accessible:** Access to the software is currently restricted because of the ongoing clinical trial and potential intellectual property rights, both at individual and institutional levels. The University of Manchester's Innovation pathways are exploring these issues, although the current position is not known. Future access may be offered through collaboration with external researchers following the clinical trial.

- **Interoperable:** There are currently no established standards, formats or metadata practices for the images, measurements or reports. The clinical trial is intended to be the beginning of a process towards greater interoperability, but this remains an area for future development.

- **Reusable:** External researchers interested in using Capilytics may potentially be invited to collaborate with the project team. Such collaboration could provide access to user guides, technical documentation and example data. However, this is a prospective arrangement rather than an established open-access or reuse policy.

## Documentation

The acquisition application includes a user guide within its built-in help system. Training materials have also been developed in the form of slide decks, which were presented to potential users when the clinical trial began.

Installation and deployment procedures have been documented, particularly the processes used to install the system on the laptops supplied to clinicians. These documents are intended to provide a starting point for adapting deployment if the system is used more widely. Each GitHub repository also has its own README, written for developers joining the project to maintain or extend the code. Code documentation is additionally considered as part of the review process.

## Sustainability

In the immediate future, the original researcher is expected to continue maintaining and supporting Capilytics. Longer-term arrangements have not yet been established and will depend substantially on the results of the clinical trial and the decisions of both the technical group and the wider collaboration.

There are plans relating to commercialisation, wider NHS adoption, regulatory approval and potentially a spinout, although the details and current status of these activities are not yet known. The potential for wider use will therefore depend on the clinical trial outcomes, the regulatory and intellectual property position, and the arrangements that can be established for ongoing support and maintenance.

## References

The public NIHR funding award record for the project is available at:

- NIHR award NIHR204551: https://www.fundingawards.nihr.ac.uk/award/NIHR204551

Further publications, presentations and other relevant research outputs are to be identified with the Capilytics technical team.

---

# Appendix 1: Lessons Learned

One of the main challenges encountered during the project was the unreliability of internet connections at some clinical centres. The pilot study revealed that connectivity could be significantly worse than anticipated. This required changes to the architecture so that image acquisition could continue independently of an internet connection, with images and associated information stored locally and synchronised with the cloud service when connectivity became available. On future projects, the team would consider the infrastructure in which the software will operate, particularly the possibility of offline use, much earlier in the design process.

The pilot study also demonstrated the importance of designing acquisition guidance around the behaviour of real users. Clinicians can make mistakes when first using a system, particularly when they have limited previous experience of capillaroscopy. The team adapted the image acquisition guidance following the pilot to address common mistakes and provide more support through the process. This reinforced the value of observing users in practice and refining software in response to their needs.

The contribution of RSEs was another important lesson. The amount of development work required was beyond what one researcher could deliver alone, making RSE involvement essential. The researcher and technical team particularly appreciated the collaborative ways of working developed with the RSEs, informed by the agile processes used by the central RSE team at Manchester.

Finally, the project highlighted the importance of understanding regulatory requirements before substantial development begins. Teams embarking on similar work should establish which medical device classification is likely to apply and understand the relevant medical device standards from the outset. These requirements can influence software development practices, documentation and quality management throughout a project.

# Appendix 2: Impact

The pilot study provided early evidence of improvements in clinicians' ability to acquire suitable images. Clinicians generally improved their image acquisition over time and reported that the system was usable and did not take too much time. These findings provide encouraging evidence about the feasibility of supporting clinicians who may have limited previous experience of capillaroscopy.

The project has also introduced capillaroscopy to some clinical centres that, as far as is currently known, had not previously acquired capillaroscopy images. This means that these centres are beginning to incorporate a new potential process into the range of services they can offer. The images collected during the clinical trial will also provide data that can inform future versions of the AI models.

More broadly, the project has demonstrated how effectively research software engineering and medical imaging communities can work together to develop a complex research software system. The contribution of RSEs has been an important part of making the development work achievable.

It is too early to establish the impact on patient outcomes, wider clinical practice or NHS costs. Improved access to capillaroscopy, earlier diagnosis and better patient outcomes remain potential future benefits rather than demonstrated results of the current work.