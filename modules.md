- Environment modules allow you to dynamically modify your shell environment to load or unload software packages and their dependencies. They are widely used on HPC clusters to manage multiple versions of compilers, libraries, and applications.
- Commands
  ```
  module list #list all loaded module
  module avail #show all available module
  module load <module1> <module2> ... #to load modules
  module remove <module_name> [<module_name2> ...]
  ```
  
- Example: Load modules for compiling MPI application with OpenBLAS optimizations:
  ```
  module load shared
  module load gcc/9.2.0
  module load openmpi/gcc/64/1.10.7
  module load openblas
  module load openblas/dynamic/0.2.20
  mpicc -o myapp myapp.c
  ```
  
- Ordinary users should load shared module by default:
  ```
  module load shared
  ```
  Root users should avoid loading shared automatically if shared storage is not available at login, as it can block root logins.
  
- On clusters without external shared storage, root can safely add shared module at login:
  ```
  sudo module initadd shared
  ```
  
- Users can also set modules to load automatically at login:
  ```
  module initadd <full_module_path>
  ```
- Setting Up Default Modules for All Users
  - Administrators can define a default set of modules that load automatically whenever any user logs into the cluster. This ensures consistency and avoids every user having to manually load frequently used modules.
  - Understanding User vs Administrator Default Modules
    - Users: Can set their own default modules using:
      ```
      module initadd <module_name>
      ```
    - Administrators: Can set default modules for all users by:
      - Adding module load commands to a profile script executed at login.
      - Using a meta-module (default-environment) that loads multiple modules at once.
  - Method 1: Using a Login Script in /etc/profile.d
    - Create a script file, e.g., `/etc/profile.d/userdefaultmodules.sh`.
      ```
      vim /etc/profile.d/userdefaultmodules.sh
      ```
    - Add the desired modules to load automatically:
      ```
      module load shared
      module load slurm
      module load gdb
      ```
    - Now, whenever a user logs in using a bash login shell, these modules are loaded automatically.
  - Method 2: Using a Meta-Module (default-environment)
    - Instead of hardcoding modules in a script, you can create a meta-module that defines the default environment.
    - Step 1: Create the default-environment module
      - Example location: /cm/shared/modulefiles/default-environment
        ```
        #%Module1.0######################################################
        ## default modulefile
        ##
        proc ModulesHelp { } {
            puts stderr "\tLoads default environment modules for this cluster"
        }
        module-whatis "adds default environment modules"
        
        # Add default modules here
        module add shared slurm gdb
        ```
    - Step 2: Load the meta-module in a profile script.
      - Update /etc/profile.d/userdefaultmodules.sh to load the meta-module silently:
      ```
      module load -s default-environment      
      ```
      - -s → Load silently to avoid displaying messages on login.
      - Benefits: Any future changes to default-environment automatically propagate to all users.
  
