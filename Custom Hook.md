```
import { useState, useEffect } from 'react';

function useFetchActivities(getActivitiesFunc, ...params) {
  const [activities, setActivities] = useState([]);

  const fetchActivities = async () => {
    const activities = await getActivitiesFunc(...params);
    setActivities(activities);
  };

  useEffect(() => {
    fetchActivities();
  }, []);

  return activities;
}

export default useFetchActivities;
```


It replaces :

```
const [activities, setActivities] = useState([]);

const fetchActivities = async () => {
  const activities = await Activity.getAll();
  setActivities(activities);
};

useEffect(() => {
  fetchActivities();
}, []);
```