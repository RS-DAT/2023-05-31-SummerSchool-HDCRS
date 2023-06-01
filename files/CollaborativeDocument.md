![](https://i.imgur.com/iywjz8s.png)


# Collaborative Document

2023-05-31 - High Performance and Disruptive Computing in Remote Sensing (HDCRS) Summer School 

Welcome to the Collaborative Document for the lecture **Remote Sensing Data Analysis using HTC/HPC Systems**.

This Document is synchronized as you type, so that everyone viewing this page sees the same text. This allows you to collaborate seamlessly on documents.

----------------------------------------------------------------------------

This is the Document for today: https://tinyurl.com/2023-05-31-hdcrs
 
 
## ⚖️ License

All content is publicly available under the Creative Commons Attribution License: [creativecommons.org/licenses/by/4.0/](https://creativecommons.org/licenses/by/4.0/).

## 🙋Getting help

To ask a question, just raise your hand.

If you need help from a helper, place a pink post-it note on your laptop lid. A helper will come to assist you as soon as possible.

## 🖥 Lecture website

[link](https://www.hdc-rs.com/summer-schools/2023/lecture-for-topic-2)

## 🛠 Setup

* Local system requirements (see [detailed setup instructions](https://github.com/RS-DAT/2023-05-31-SummerSchool-HDCRS/blob/main/setup.md)):
    * SSH
    * Python 3
    * Python libraries: `fabric` and `decorator` 
    * RS-DAT deployment scripts: [link](https://github.com/RS-DAT/JupyterDaskOnSLURM/archive/refs/heads/workshops.zip) 


* Download the SSH key for Spider using the link on the printout. You will receive the printout at the begining of day 3.

**The following steps will be run jointly *DURING* the session accompanied by further explanation and require the account information on the printout. However, we provide them here for reference.** 

* Access the `JupyterDaskOnSLURM-workshops` folder in your terminal  (you might have to modify the path below): 
  ```shell
  cd ~/Downloads/JupyterDaskOnSLURM-workshops
  ```
* Edit the configuration file `config/platforms/platforms.ini` with your account information:
    * You can use `nano` (e.g. `nano config/platforms/platforms.ini` to start editing, Ctrl+X to quit); 
    * Replace the user name `stursdat-XX` with your user ID (e.g. `stursdat-01`);
    * Replace the SSH key path `/path/to/the/ssh-key/rsa_stursdat_XX` with the actual file path (e.g. `/Users/fnattino/Downloads/rsa_stursdat_01`).   
* Start a Jupyter session on Spider by running the following command:
  ```
  python runJupyterDaskOnSLURM.py --uid spider-stursdat --mode run
  ```
  Jupyter Lab should open up in your browser! 


## 👩‍🏫👩‍💻🎓 Speakers

Meiert W. Grootes, Francesco Nattino, Pranav Chandramouli, Lodewijk Nauta


## 🗓️ Agenda


![](https://hackmd.io/_uploads/rkSyfY7I2.png)



## 👩‍💻👩‍💼👨‍🔬🧑‍🔬🧑‍🚀🧙‍♂️🔧 Roll Call
Name/ pronouns (optional) / job, role / social media (twitter, github, ...) / background or interests (optional) / city

**data removed**

## 🧠 Collaborative Notes

#### RS-DAT Setup:

Go to workshop directory (the below command works if it is stored in your home directory):
```
cd ~/JupyterDaskOnSLURM
``` 

To see the help information:
```
python runJupyterDaskOnSLURM.py --help
```

Edit the config file that can be found under `config/platforms/platform.ini` to (as an example):

[spider-stursdat]
platform = spider
host = spider.surf.nl
user = stursdat-34
keypath = /home/cpranav93/rsa_stursdat_34

where 'cpranav93' is my ubuntu username

To set-up the notebook on the HTC:
```
python runJupyterDaskOnSLURM -u spider-stursdat -m run
```

If port forwarding does not work or if you have the no runnable browser found issue, run the following:
```
ssh -i /path/to/private/ssh/key -N -L 8889:wn-ca-19:8588 stursdat-XX@spider.surf.nl
```



The github repo for the hands-on session is
https://github.com/RS-DAT/2023-05-31-SummerSchool-HDCRS

paste this link into the `clone a repository` entry field on the git lab extension

if you don't have the git lab extension:

open a terminal window from the launcher window
 then enter:
 `git clone https://github.com/RS-DAT/2023-05-31-SummerSchool-HDCRS`
 
 which should clone the repo.
 
 Now we discuss Dask, a python library fro parallel and disttributed computing.
 
 Dask has various abstractions allowing you to interact with data: Dask Array, Dataframe, but also more low level.
 
 It tranlsates all of these and all actions on them into a task graph.
 
Dask can run on multiple cores of a single cpu, up to multiple nodes with multiople cores each of a HPC system.

Dask works by paralleizing all non-connected elements of the task graph, allowing you to speed up your processing massively.

There are a few requisites though: 
1) the algorithm you want to run must be amenable to paralleization
2) The data volume should not be too small, so that overhead (required for task distribution) does not dwarf computational time

### Task Graphs

#### Dask delayed


we use the delayed decorator to covert the function to a lazy execution
```
from dask.delayed import delayed

@delayed
def add(x, y):
    priint(x, "+", y)
    return x + y
```




```
a_p = add(1,2)
``` 
`a_p` is a so-called "promise". This promise represents instructions for the operation allowing the operation to be performed, but nothing is executed yet. Other opertaions however can see this promise and will understand what the output will look like. This is nevertheless a regular python function

`b_p = add(a_p,3)`

`c_p = add(a_p,b_p)`

now we want to visualize the dask task graph

`import dask`

`dask.visualize(c_p, rankdir="LR")`
the latter argument just ensures that the graph is displayed from left to right (beginning to end)


`c_p.compute()`
this actually executes the task graph, runs all functions and returns the result.

This was for the case of generic python functions. Now we will consider arrays

#### dask arrays
Rather than having to implement delayed operations over arrays, Dask already offers this for you , allowing lazyy operations over chunked (i.e.subdivided) arrays

first we import daskarray. Dask arrays mimic the Numpy api
`import dask.array as da`

`x=da.random.random((2000,1000), chunks=(500,500))`

Visualize in rich format by running
`x`

now we define operations on the array(s)
y is the dot product between `x` and it's transposed `x.T`

note that no calculation have taken place yet.

`y=da.dot(x,x.T)`

now we can compute the mean value for the resultiing array
`z=y.mean()`

`dask.visualize()`
can then be used to visualize the task graph, which can quickly grow in size and complexity. All this is handled by dask in the background.

To actually execute the graph we run

`z.compute()`

Dask arrays are a very good way to store and represent grided (multi-dimensional) data. However, as simple arrays, dask arrays rely soley on their indicees.

However, real data (generally) has dimensions with physical meaning. 

#### Xarray

Xarray provides you with this. It uses dask arrays to store/represent data, but provides a rich representation allowing you to use the physical values of the data as coordiantes to seclet and manipulate the data

`raster_path = '/project/stursdat/Data/RS-DAT/sentinel-2-l2a_AMS_2023-04/2023/4/30/S2B_31UFU_20230430_0_L2A/B02.tif'`

`import rioxarray`
which is the adapter enabling xarray to levarage `rasterio` (and hence `gdal`) to open files as xarray objects

`raster = rioxarray.open_rasterio(raster_path, chunks ={'x':2048, 'y':2048})`

Note: If the chunk argument is NOT provided this will result in a Numpy array, which will be loaded immediately. Using the chunk argument will result in an xarray dataarray, i.e. an xarray object backed by dask arrays

the file can now be inspected as

`raster`

again, nothing has happend yet, even the file has not been loaded. These are still instructions.

we can then define , e.g. 

`raster_max = raster.max()`

which is only computed when running

`raster_max.compute()`

NB: Dask always returns arrays (even if they are zero diemnsional, i.e. a float). To retrieve just the values one can index it out ( e.g. []) or run
raster_max.compute().values

Chunk sizes should be big enough to not be inefficient, but not to large. This will depend on use case and is best defined by a bit of trial an error, a rule of thumb is that ~100 MB/chunk is a good size, which can be seen by inspecting the object

### Executing Graphs

#### multi-threading/processing
Use of multi threading is the default. This can be explicittly specifed by setting the sheduler argument as below.The `n_workers` argument allows the number of workers to be specified.

Alternatively, multiple processes can also be used as
raster_max.compute(sheduler="process", n_workers=2)


`raster_max.compute(scheduler="threads", n_workers=2)`
NB processes do not share memory, so are a bit slower in a setting where threads can share memory (cores on one cpu), however, processes allow for further scaling.
processes can only share data by serializing data, whereas threads can read from the same memory namespace. However, use of multiple threads is ONLY possible on the scale of the single CPU the threads are on.

Processes allow for further scaling as they can also be distributed over multiple CPUs and nodes.

For this we make use of dask's distributed scheduler

#### Dask distributed

In this example we have scaled the cluster via the GUI in the Jupyter lab dask extension. The cluster can be dragged into the note book which results in a cell echoing the commands

`from dask.distributed import Client

client = Client("tcp://10.0.2.120:39077")
client
`

now we can use the cluster to perform the comptations

`raster_max.compute()`

and use the dask dashboard to show the task stream and see what is happening (and also where bottlenecks may be). As a result this allows you to debug and optimize your workflow for execution on the cluster.

Note: the distributed scheduler becomes the default when imported and registerd, i.e. replacing the multithread scheduler.

PLEASE REMEMBER: we are running on shared resources. When you're done, please shutdown your cluster! 

via the File->shutdown option of the Jupyter lab interface



## Data Retrieval

### STAC: APIs vs. Static catalogs

STAC resources can be dynamic or static catalogs

example of dynamic resource
get stac cleint

`import pystac_client`

set api url for stac resource
`stac_api_url = "https://earth-search.aws.element84.com/v1"`

connect client
`client = pystac_client.Client.open(stac_api_url)`

checkout the resource
`client`

look which colletions are available from the API
```
for collection i cliennt.get_collections():
    print(collection)
```

`client.search?` lets you peruse properties which are containd and which selections can be made on

However, not all data is available via such a dynamic service.

Static catalogs are also possible (remotely) or you can make your own over data you collect.

First we check out the structure of a static catalogue

import pystac tool
`import pystac`


set root directory
`stac_root_dir = "/project/stursdat/Data/RS-DAT/sentinel-2-l2a_AMS_2023-04"`


inspect nested catalog structure using the bash built in `tree` command
`!tree $stac_root_dir`


but we can also use pystac tools tto insopect the catalog
`catalog = pystac.Catalog.from_file(f"{stac_root_dir}/catalog.json")`


`catalog.describe()`
gives a top level overview over the catalog

we can retrieve the links tto all the items in the catalog, i.e. nested data objects/files
`for item in catalog.get_all_items():
    print(item)`
 
 and then inspect the metadata
`item.properties`

`item.assets`

## Constructing a catalog

### Daymet v4

Sometimes it is preferable to retrieve data and use HPC rather han work on the cloud. In the following we show homw to retrieve only partially structured data from a remite archive, building a structured STAC catalog for further use

The cells below contain pre defined functions which were run
![](https://hackmd.io/_uploads/B1q9kR4L2.png)

![](https://hackmd.io/_uploads/SJMs1AELn.png)

Now we create an empty catalog struture. We will then loop over the sub-regions as defined above, and then loop over the years covered by the data set, as well as the data variables, creating STAC catalog items for each subsequently adding the items to the catalog
```
import datetime
import shapely


def create_item(region, year):
    """ Create STAC Item for the given region and year. """
    bbox = REGIONS[region]["bbox"]
    geom = shapely.box(*bbox)
    return pystac.Item(
        id=f"{region}-{year}",
        geometry=shapely.geometry.mapping(geom),
        bbox=bbox,
        datetime=datetime.datetime(year, 1, 1),
        properties={"region": region},
    )

def create_asset(region, year, parameter):
    """ Create STAC Asset for . """
    href = get_daymet_file_url(region, parameter, year)
    return pystac.Asset(href)
```

`catalog = pystac.Catalog(id="daymet-daily-v4.5", description="Daymet Daily v4.5")`

```
for region in REGIONS.keys():
    for year in REGIONS[region]["year_range"]:
        item = create_item(region, year)
        for parameter in PARAMETERS:
            asset = create_asset(region, year, parameter)
            item.add_asset(parameter, asset)
        catalog.add_item(item)`
```
we loop over each region
--- loop over each year
    create the catalog item
    --- loop over all parameterd
        attached the prameter to the item
    attach it all itemns to catalog 

inspect the resulting catalog

`catalog.describe()`

we can generate a subcatalog structure (e.g. for each region) with the existing catalog

`catalog.generate_subcatalogs("${region}")`

Note: The `$` is pystac syntax which lets pystac expand the variable and loop over its individual values

`catalog.describe()`

now we can extract a data item using pystac, e.g. Puerto Rico for 2022, and all nested catalog entries
`catalog.get_item("pr-2022",recursive=True)`

save the catalog
`catalog.normalize_and_save("daymet_daily")`

BUT! We haven't actually downloaded any data yet! the assets currently only contain the appropriate urls pointing to the remote data source.

Assume that for what we want to do, we only need part of the data. We can now retrieve only the relevant information storiing it locally (both local file systems and dCache mass storage is suppoorted) for further processing and leave the rest as indexed remote data sources, to be retrieved only when needed. 


Now we can download the part we need

`from stac2dcache.utils import copy_asset`


we select the haawai'i sub-catalog
`catalog_hi = catalog.get_child("hi")`

and then retirieve the tmin data to local storage and update the links for the retrieved data to pint at the local copy instead of the remote source. All other parameters are still remote
```
copy_asset(
    catalog_hi,
    asset_key="tmin",
    max_workers=2,
    update_catalog=True,
)
```

`catalog_hi.save()`

## Scaling workflows on HPC

We will now running through a real world use case of calculating spring indicees. The actual scientific project used all of the Daymet4 data at its highest resolution across the entire continental US, spanning several decades.
In this demo we will using a subset of the data, but without any further simplifications. In other words, running this same workflow for the full set would only take longer, which couldd be compensated by using more workers thaat we can provide you with today.

import required libraries
```
import dask.array as da #Parallel Processing library
import fsspec #Data access library
import numpy as np #Scientific computing with python
import pyproj #Projections and CRS transformation library
import pystac #STAC interface with python 
import rioxarray #Raster i/o with python
import stac2dcache #Interfacing STAC with dCache
import xarray as xr #Multi-dimensional arrays in Python
```
for this demo we are going to pick a single year

start- and end date are based on knowledge of growiing season (i.e. not in the winter), and serve to reduce computational load
```
#set parameters
year = 1980
startdate = 1
endddate = 300
```
set paths
```
# dCache project root path
root_urlpath = "dcache://pnfs/grid.sara.nl/data/remotesensing/disk"

catalog_urlpath = f"{root_urlpath}/daymet-daily-v4/catalog.json"
output_urlpath = f"~/spring-index-models.zarr"
```
set parameters 
```
BASE_TEMP_FAHRENHEIT = 31.

HOURS = xr.DataArray(
    data=da.arange(24), 
    dims=("hours",),
)

DAYS = xr.DataArray(
    data=da.arange(startdate, enddate+1),
    dims=("time",),
)
```
```
LEAF_INDEX_COEFFS = xr.DataArray(
    data=da.from_array(
        [
            [3.306, 13.878, 0.201, 0.153],
        ],
        chunks=(1,-1)
    ),
    dims=("plant", "variable"),
    coords={"plant": ["lilac"]}
)

LEAF_INDEX_LIMIT = 637
```
we have preimplemeted (some) of the required functions.

Note: well know methods (almost all!) from e.g. Numpy such as `where` are also availble for xarray objects!

![](https://hackmd.io/_uploads/HJ_GaC4In.png)

![](https://hackmd.io/_uploads/BJWmaCN8h.png)

![](https://hackmd.io/_uploads/Skam6C4Ih.png)


Now we can open the input data catalog

```
#Define catalog
catalog = pystac.Catalog.from_file(catalog_urlpath)
```
`catalog.describe()`
to see all the data, not only the preview

drag and drop scheduler into notebook as was shown this morning


WE ARE USING 3 WORKERS, 6 CORES


`client.wait_for_workers(n_workers=3)`
this makes the client wait untill three workers have joined the cluster


now we define a function to open all the files from the catalog as a single dataset
```
def open_dataset(urlpaths, **kwargs):
    """
    Open the remote files as a single dataset. 
    """
    
    ofs = fsspec.open_files(urlpaths, block_size=4*2**20)
    return xr.open_mfdataset(
        [of.open() for of in ofs],
        engine="h5netcdf", 
        decode_coords="all",
        drop_variables=("lat", "lon"),
        **kwargs
    )
```

with this funtion in hand we extract the urls of the fles from the catalog
```
# Extract urlpaths to Daymet files from catalog
item = catalog.get_item(f"na-{year}", recursive=True)
hrefs = [
    item.assets[var].get_absolute_href() 
    for var in ("tmin", "tmax", "dayl")
]
```


Now we can pen all the files as single dataset in the form of  dask array backed xarray object chunked into blocks of 1000 by 1000 in x,y and 5 in time
```
# Open files as a single dataset, using a chunked Dask array
ds_orig = open_dataset(hrefs, chunks={"time": 5, "x": 1000, "y": 1000})
ds_orig
```

Note: when defining chunks do pay attention to the native size of your files. a good match may improve performance. However, chunk size is a parameter to play around with.

The dataset we have is too large for the purposes of this demo. we will therefore coarsen it by a factor of 20 in x and y for this exercise.


Note: No data loading has actually taken place yet! This is all being expressed as a dask task graph, being prepared for lazy executtion when triggerd. 

```
ds = ds_orig.coarsen({"x": 20, "y":20}, boundary="trim").mean()
ds
```

We now plot s small slice of the dataset to see whether everything makes sense. Dask allows us to only read this small piece of data.  
```
# Plot a slice of the dataset
ds["tmax"].isel(time=0).plot.imshow()
```


Now we can define a preprocessing function for the dataset
```
def preprocess_dataset(ds, startdate, enddate):
    """
    Subset the input dataset and make necessary conversions.
    """
    
    # Select time range for GDH calculation
    ds = ds.isel(time=slice(startdate-1, enddate))
    
    #spatial selection could be dine here using rio.clip_box for example
    
    # Convert temperatures to Fahrenheit
    tmax = ds["tmax"] * 1.8 + 32
    tmin = ds["tmin"] * 1.8 + 32

    # Convert daylength from seconds to hours
    dayl = ds["dayl"] / 3600

    return tmax, tmin, dayl
```

Extract temporal/spatial ranges, unit conversion
```
tmax, tmin, dayl = preprocess_dataset(ds, startdate, enddate)
```

after preprocessing the data we can plot the same slice
```
# Plot same slice after pre-processing
tmax.isel(time=0).plot.imshow()
```

We now have the data reading and preprocessing in place. We can calculate the model parameters with the functions we defined earlier.
At the same time, because we want to loop over all the days for a single location in our processing, we will rechunk accordingly. This will impriove the performance of the calculation. Choosing the optimal chunk size, is again, a matter of trying sizes out

```
# Calculate GDH and rechunk to have single chunk along time axis
gdh = calculate_gdh(dayl, tmin, tmax)
gdh = gdh.chunk({"time": enddate-startdate+1, "x": 100, "y": 150})
gdh
```
In the interest of time, we will skip the calculation for the plottting, moving directly to the first leaf index calculation

```
# Fist leaf index
dde2, dd57, mds0, synop = calculate_leaf_predictors(gdh)
first_leaf = calculate_first_leaf(dde2, dd57, mds0, synop)
```

Now we trigger the calculation of the entire workflow as we defined above
```
# Trigger calculation of spring indices
first_leaf = client.persist(first_leaf)
```
Note: .persist() is non-blocking, that means, that after you have executed the command, all process will be running, but controll of the note book will already return. Any new command issued that needs the data being persisted will, however, block untill the data is available


```
# Plot the first-leaf date
first_leaf.plot.imshow(col="plant")
```


define function to save cacluted data product using Zarr

```
def save_to_urlpath(first_leaf, urlpath, group):
    """
    Save output to urlpath in Zarr format. 
    """
    
    fs_map = fsspec.get_mapper(urlpath)
    ds = xr.Dataset({
        f"first-leaf": first_leaf, 
    })
    ds.to_zarr(fs_map, mode="w", group=group)
```

and save to zarr

```
# Rechunk and save to storage
save_to_urlpath(
    first_leaf.chunk({"plant": 1, "x": 500, "y": 500}),
    output_urlpath, 
    f"{year}",
)
```

### Deployment

Clone repository with configuring scripts:
```shell
git clone https://github.com/RS-DAT/JupyterDaskOnSLURM
```

## 📚 Resources
A nice [Blog](https://blog.dask.org/2021/11/02/choosing-dask-chunk-sizes) on optimal dask chunk size - 'Choosing good chunk sizes in Dask'

Link to the daymet data: [Daymet Data](https://daac.ornl.gov/cgi-bin/dsviewer.pl?ds_id=2129)
