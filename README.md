# MOLLER Experiment JANA2 Application

This project is a JANA2-based application for processing MOLLER experiment data using EVIO format input files. The application includes custom event sources, processors, and factories for handling FADC250 detector data.

## Dependencies

This project requires two main dependencies that must be built separately:

### 1. JANA2 Framework

JANA2 is the core framework used by this application. You need to build and install JANA2 before building this project.

#### Building JANA2

```bash
# Clone the JANA2 repository
git clone https://github.com/JeffersonLab/JANA2.git JANA2
cd JANA2

# Configure and build JANA2
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug -DCMAKE_INSTALL_PREFIX=`pwd`
cmake --build build --target install -j 20
```

### 2. EVIO Library

The EVIO library is required for reading EVIO format data files.

#### Building EVIO

```bash
# Clone the EVIO repository
git clone --branch evio-6-dev https://github.com/JeffersonLab/evio/
cd evio; mkdir build
cmake -S . -B build
cmake --build build --target install --parallel
```

## Building the MOLLER Application

Once you have built both JANA2 and EVIO, you can build this application:

### Prerequisites

- CMake 3.16 or higher
- C++17 compatible compiler
- Built and installed JANA2 (see above)
- Built EVIO library (see above)

### Build Instructions

1. **Configure the build:**
   ```bash
   cmake -S . -B build -DCMAKE_PREFIX_PATH=/path/to/your/JANA2 -DCMAKE_BUILD_TYPE=Debug
   ```

   **Important:** Replace `/path/to/your/JANA2` with the actual path to your JANA2 installation directory.

2. **Build the application:**
   ```bash
   cmake --build build
   ```

### Customizing Build Paths

If your JANA2 and EVIO installations are in different locations, you'll need to modify the `CMakeLists.txt` file to point to the correct paths:

1. **For JANA2:** Update the `CMAKE_PREFIX_PATH` in the cmake command to point to your JANA2 installation directory.

2. **For EVIO:** Edit the `CMakeLists.txt` file and update the `EVIO_LIB_PATH` variable on line 9:
   ```cmake
   set(EVIO_LIB_PATH /your/custom/path/evio/Linux-x86_64)
   ```
   
   This single variable controls all EVIO-related paths:
   - Include directory: `${EVIO_LIB_PATH}/include`
   - Library directory: `${EVIO_LIB_PATH}/lib64`
   - Runtime library path: `${EVIO_LIB_PATH}/lib64`

## Project Structure

- `moller.cc` - Main application entry point
- `JEventSource_EVIO.cc/.h` - Custom EVIO event source
- `JEventProcessor_MOLLER.cc/.h` - Main event processor for MOLLER data
- `JFactory_FADC250.cc/.h` - Factory for FADC250 detector data
- `FADC250.h` - Data structure for FADC250 hits
- `EvioEventWrapper.h` - Wrapper for EVIO event data

## Usage

After building, run the application with:

```bash
./build/moller [jana_options] <evio_file1> [evio_file2] ...
```

The application will process the specified EVIO files and output results according to the configured processors.

## Build Types

- **Debug:** Use `-DCMAKE_BUILD_TYPE=Debug` for development and debugging
- **Release:** Use `-DCMAKE_BUILD_TYPE=Release` for optimized production builds

## Troubleshooting

1. **JANA2 not found:** Ensure `CMAKE_PREFIX_PATH` points to your JANA2 installation directory
2. **EVIO libraries not found:** Check that the EVIO library paths in `CMakeLists.txt` are correct
4. **Runtime library errors:** Ensure the EVIO library path is correctly set in the `BUILD_RPATH` property

## Example Complete Build Sequence

```bash
# 1. Build JANA2
git clone https://github.com/JeffersonLab/JANA2.git JANA2
cd JANA2
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug -DCMAKE_INSTALL_PREFIX=`pwd`
cmake --build build --target install -j 20
cd ..

# 2. Build EVIO
git clone --branch evio-6-dev https://github.com/JeffersonLab/evio/
cd evio; mkdir build
cmake -S . -B build
cmake --build build --target install --parallel
cd ..

# 3. Build MOLLER application
cd moller_exp
cmake -S . -B build -DCMAKE_PREFIX_PATH=/path/to/JANA2 -DCMAKE_BUILD_TYPE=Debug
cmake --build build
```
