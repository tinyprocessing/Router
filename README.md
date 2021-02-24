```python
#!/usr/bin/env python

#Safir Mikhail 18.02.2021 

import os
import json
import random
import time
import numpy
import math
import matplotlib.pyplot as plt
from IPython.display import clear_output

%matplotlib inline
```


```python
class Settings:
    devices = 10 # Number of devices in the simulation
    signal_per_device_gain = 150 
    signal_per_device_zone = 100
    end_x = 800
    end_y = 800
    
settings = Settings()
```


```python
class Device:
    device_id = 0
    x = 0
    y = 0
    device_type = "mobile" #tower
    in_zone = False
```


```python
class Draw:

    def draw_once(self, devices, graph_devices):
        plt.figure(figsize=(10, 10))
        fig = plt.gcf()
        ax = fig.gca()
        
            
        for i in range(0, len(devices)):
            ax.add_artist(plt.Circle((devices[i].x, devices[i].y), settings.signal_per_device_zone, color='r', fill=False))
            
        for i in range(0, len(devices)):
            plt.plot(devices[i].x, devices[i].y, 'o', color="blue")
            plt.text(devices[i].x, devices[i].y+25, str(devices[i].device_id), fontdict=None, horizontalalignment='center', verticalalignment='center')

        for i in range(0, len(devices)):
            if (graph_devices.get(devices[i]) is None):
                pass
            else:
                for k in range(0, len(graph_devices.get(devices[i]))):
                    reach_device = graph_devices.get(devices[i])[k]
                    plt.plot([devices[i].x, reach_device.x], [devices[i].y, reach_device.y], 'ro-')
            
        plt.plot(0, 0, 'o')
        plt.plot(1000, 1000, 'o')
        
        plt.title("Routing")
        plt.grid(False)
        plt.show();

    
    def draw(self, devices, graph_devices):
        
        clear_output(wait=True)
        plt.figure(figsize=(10, 10))
        
        
        fig = plt.gcf()
        ax = fig.gca()
        
        for i in range(0, len(devices)):
            ax.add_artist(plt.Circle((devices[i].x, devices[i].y), settings.signal_per_device_zone, color='r', fill=False))
            
        for i in range(0, len(devices)):
            plt.plot(devices[i].x, devices[i].y, 'o', color="blue")
            plt.text(devices[i].x, devices[i].y+25, str(devices[i].device_id), fontdict=None, horizontalalignment='center', verticalalignment='center')

        for i in range(0, len(devices)):
            if (graph_devices.get(devices[i]) is None):
                pass
            else:
                for k in range(0, len(graph_devices.get(devices[i]))):
                    reach_device = graph_devices.get(devices[i])[k]
                    plt.plot([devices[i].x, reach_device.x], [devices[i].y, reach_device.y], 'ro-')
            
        plt.plot(0, 0, 'o')
        plt.plot(1000, 1000, 'o')
        
        plt.title("Routing")
        plt.grid(False)
        plt.show();
```


```python
class Generator:
    
    def collision(self, devices):
        collision_main = {}
        collision_main_device = {}
        
        for i in range(0, len(devices)):
            device_current = devices[i]
            collision_array = [device_current.device_id, []]
            collision_array_device = [device_current, []]
            
            for j in range(0, len(devices)):
                device_to_reach = devices[j]
                if (device_current == device_to_reach):
                    continue;
                distance = math.sqrt( ((int(device_current.x)-int(device_to_reach.x))**2)+((int(device_current.y)-int(device_to_reach.y))**2) )
                    
                if (distance < (settings.signal_per_device_zone * 2)):
                    collision_array_device[1].append(device_to_reach
                                                    )
                    collision_array[1].append(device_to_reach.device_id)
#                     print("device " + str(device_current.device_id) + " reached " + str(device_to_reach.device_id) + " with dis " + str(distance))

            if (len(collision_array[1]) > 0):
                collision_main[device_current.device_id] = collision_array[1]
                collision_main_device[device_current] = collision_array_device[1]
        return collision_main, collision_main_device
    
    def walk(self, devices):
        
        devices_new = []
            
        for i in range(0, len(devices)):
            x = devices[i].x
            y = devices[i].y
            n_x, n_y = random.randint(-15, 15), random.randint(-15, 15)
            x += n_x
            y += n_y
            
            device = Device()
            device.device_id = devices[i].device_id
            device.x = x
            device.y = y
            
            devices_new.append(device)
            
        return devices_new
            
    
    def start(self):
        devices = [] #Device array with classes
        
        for i in range(0, settings.devices):
            x, y = random.randint(100, settings.end_x),  random.randint(100, settings.end_y)
            
            device = Device()
            device.device_id = i
            device.x = x
            device.y = y
            
            devices.append(device)
            
        return devices
        pass
```


```python
class Router:
    
    generator = Generator()
    draw = Draw()
    devices = []
    graph = {}
    graph_devices = {}
    
    def build(self):
        plt.ion()
        self.devices = self.generator.start()
        self.graph, self.graph_devices = self.generator.collision(self.devices)
        print(self.graph)
        self.draw.draw_once(self.devices, self.graph_devices)
        
#         while True:
#             self.devices = self.generator.walk(self.devices)
#             self.graph, self.graph_devices = self.generator.collision(self.devices)
# #             self.draw.draw_once(self.devices, self.graph_devices)
#             self.draw.draw(self.devices, self.graph_devices)
#             plt.pause(0.5)
        pass
    
    def start(self):
        self.build()
        pass
```


```python
router = Router() # call public class
settings.devices = 10
settings.signal_per_device_zone = 100
router.start()

print(router.graph)
```

    {0: [6], 1: [4], 2: [5, 7], 3: [8, 9], 4: [1, 5], 5: [2, 4, 7], 6: [0], 7: [2, 5], 8: [3], 9: [3]}



    
![png](output_6_1.png)
    


    {0: [6], 1: [4], 2: [5, 7], 3: [8, 9], 4: [1, 5], 5: [2, 4, 7], 6: [0], 7: [2, 5], 8: [3], 9: [3]}



```python
def dfs_paths(graph, start, goal):
    stack = [(start, [start])]  # (vertex, path)
    while stack:
        (vertex, path) = stack.pop()
        for next in set(graph[vertex]) - set(path):
            if next == goal:
                yield path + [next]
            else:
                stack.append((next, path + [next]))
```


```python
print(list(dfs_paths(router.graph, 2, 1)))
router.graph.get(100) is None
```

    [[2, 6, 4, 8, 1], [2, 6, 4, 8, 9, 7, 0, 5, 1], [2, 6, 4, 8, 9, 5, 1], [2, 6, 4, 8, 9, 0, 5, 1], [2, 6, 4, 8, 5, 1]]





    True




```python

```
