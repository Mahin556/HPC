Perfect 👍 Let’s go step by step with a **complete tutorial on R packages** – what they are, how they work, and how to install/manage them.

---

# **R Packages – Complete Tutorial**

---

## **1. What is an R Package?**

* A **package** in R is a collection of functions, datasets, and documentation bundled together.
* Packages extend the capabilities of base R (which comes with a limited set of functions).
* Example:

  * **ggplot2** → advanced data visualization
  * **dplyr** → data manipulation
  * **caret** → machine learning

You can think of packages in R just like **libraries in Python**.

---

## **2. Where do R Packages Come From?**

* **CRAN (Comprehensive R Archive Network)** → official repository, most common.
* **Bioconductor** → specialized for bioinformatics.
* **GitHub / GitLab** → for latest development versions.
* **Local / custom packages** → built internally in organizations.

---

## **3. Installing R Packages**

### (a) From CRAN (most common)

```R
install.packages("ggplot2")
```

* Installs the package **from CRAN** into your local library.
* Can install multiple at once:

```R
install.packages(c("dplyr", "tidyr", "stringr"))
```

---

### (b) From Bioconductor (for bioinformatics)

```R
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("GenomicFeatures")
```

---

### (c) From GitHub (latest dev versions)

First install `devtools` (or `remotes`):

```R
install.packages("devtools")
```

Then:

```R
devtools::install_github("tidyverse/ggplot2")
```

---

### (d) From a Local File (tar.gz or zip)

```R
install.packages("path/to/package.tar.gz", repos = NULL, type = "source")
```

---

## **4. Loading Packages**

After installation, load a package into your session:

```R
library(ggplot2)
```

or

```R
require(ggplot2)
```

* `library()` → throws an error if package not found.
* `require()` → returns FALSE if not found (script continues).

---

## **5. Checking Installed Packages**

* List all installed:

```R
installed.packages()
```

* Check version:

```R
packageVersion("ggplot2")
```

* Where installed:

```R
.libPaths()
```

---

## **6. Updating Packages**

* Update one:

```R
update.packages("ggplot2")
```

* Update all:

```R
update.packages(ask = FALSE)
```

---

## **7. Removing Packages**

```R
remove.packages("ggplot2")
```

---

## **8. Getting Help About Packages**

* Help page:

```R
help(package = "ggplot2")
```

* Vignettes (long tutorials/examples):

```R
vignette(package = "ggplot2")
```

* Search:

```R
??ggplot2
```

---

## **9. Best Practices**

* Always use **LTS versions of R** (latest stable).
* Install packages in a **user library** (not system-wide) if you don’t have root.
* Use **project-specific libraries** with `renv` for reproducibility.
* Check CRAN task views: [https://cran.r-project.org/web/views/](https://cran.r-project.org/web/views/)

---

## **10. Example Workflow**

```R
# Install tidyverse (set of packages)
install.packages("tidyverse")

# Load it
library(tidyverse)

# Use a function from dplyr
data(mtcars)
mtcars %>%
  filter(mpg > 20) %>%
  select(mpg, hp) %>%
  arrange(desc(mpg))
```

---

✅ **Summary:**

* `install.packages()` → install from CRAN
* `library()` → load into session
* `update.packages()` → keep up to date
* Can also install from **Bioconductor, GitHub, local files**

---

Would you like me to also create a **one-page R Package Installation Cheat Sheet (with commands and examples)** for quick reference, similar to what I gave you for `pdsh`?
