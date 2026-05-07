# file: code/geopandas/LA_home_prices.py
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.transforms import Bbox
import geopandas as gp
import pandas as pd
zipcode_geom = gp.read_file('usa_zip/tl_2019_us_zcta510.shp')
home_prices  = pd.read_csv('Zip_zhvi_uc_sfrcondo_tier_0.33_0.67_sm_sa_mon.csv')
