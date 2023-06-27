---
title: "Data Modeling"
date: 2023-03-08T14:37:09+08:00
menu: main
weight: -98
---

Since we want to know if the chosen period of our hypothesis represents an increase in the number of misinformation/disinformation tweets regarding our chosen topic, we will use an event-detection algorithm on the tweet-frequency data we have generated in the _Data_ section so we can find the breakpoints that will match the start and end period of our hypothesis. We will use [rupture's Pelt change-point detection algorithm](https://centre-borelli.github.io/ruptures-docs/user-guide/detection/pelt/) to find the breakpoints in our time-series data:

```python

import ruptures as rpt
import plotly.graph_objects as go
import functools
from dateutil.relativedelta import relativedelta

start = datetime(2016, 1, 1)
end = datetime(2023, 1, 1) - timedelta(1)
counts = {}

while start <= end:
    counts[start.date()] = 0
    start += relativedelta(months=1)

freq = df['Date posted'].value_counts().to_dict()

for i in freq:
    new = datetime(year=i.year, month=i.month, day=1)
    counts[new.date()] += freq[i]

data = np.array(list(counts.values()))

# Parameters
MODEL = 'l1'
MIN_SIZE = 3
JUMP = 1
PEN = 3

fig, axs = plt.subplots(1, 1)

# Plot time-series data
axs.plot(counts.keys(), counts.values(),color = "#1C03DC")
axs.set_xlabel('Month')
axs.set_ylabel('Tweets')

result = rpt.Pelt(model=MODEL, min_size=MIN_SIZE, jump=JUMP).fit_predict(data, pen=PEN)

# Plot breakpoints.
first = True
for m in result[:-2]:
    bp = datetime(2016, 1, 1) + relativedelta(months=m-1)
    print(f"bp = {bp.date()}")
    if first:
        axs.axvline(x=bp.date(), color='#FE18A3', linestyle='--', label='Breakpoint')
        first = False
    else:
        axs.axvline(x=bp.date(), color='#FE18A3', linestyle='--')

# Leni's announcement of candidacy (start period of hypothesis).
axs.axvline(x=datetime(2021, 10, 7).date(), color='orange', linestyle='-', label="Leni runs for president")

# BBM's first SONA (end period of hypothesis)
axs.axvline(x=datetime(2022, 8, 25).date(), color='green', linestyle='-', label="BBM's first SONA")

plt.subplots_adjust(hspace=1.5)
plt.suptitle('Event detection using the PELT algorithm (model={}, min_size={}, jump={}, penalty={})'.format(MODEL, MIN_SIZE, JUMP, PEN))

# Create a legend for the subplot and position it outside
legend = axs.legend(loc='upper left', bbox_to_anchor=(1.02, 1))

# Adjust the layout to accommodate the legend
plt.subplots_adjust(right=0.8)

bbox_props = dict(boxstyle='round', facecolor='white', alpha=0.5)

# During the highest peak (when BBM request inhibition of Assoc. Justice Caguioa).
axs.text(datetime(2018, 11, 1), 59, 'October 2018', ha='left', va='center', fontsize=10, bbox=bbox_props)
plt.scatter(datetime(2018, 8, 1), 59, color="#1C03DC", s=30)

# Last zero frequency (when BBM took oath for presidency).
axs.text(datetime(2022, 8, 1), 0, 'June 2022', ha='left', va='center', fontsize=10, bbox=bbox_props)
plt.scatter(datetime(2022, 6, 1), 0, color="#1C03DC", s=30)

# Save graph as PNG image.
plt.savefig('data_modeling.png',transparent=True, bbox_inches='tight') 
 
```

In the _Results_ section, we show and discuss the result of the code above.