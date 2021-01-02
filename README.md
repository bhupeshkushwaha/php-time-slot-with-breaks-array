# time-slot-with-breaks-array
Get time slot between times and also check breaks time and remove from slot

```
<?php

/**
 * @param $duration
 * @param $start
 * @param $end
 * @param array $breakSlots
 * @return array
 */
function availableSlots($duration, $start, $end, $breakSlots = array()): array
{
    $periods = array();

    $startTime = strtotime($start);
    $endTime = strtotime($end);
    $addMins = $duration * 60;

    while ($startTime <= $endTime) {
        if (!empty($breakSlots)) {
            $periods[] = $startTime;
        } else {
            $periods[] = date("H:i:s", $startTime);;
        }

        $startTime += $addMins;
    }

    if (!empty($breakSlots)) {
        $periods = array_map(function ($period) use ($breakSlots) {
            foreach ($breakSlots as $slot) {
                $startSlot = strtotime($slot['start_time']);
                $endSlot = strtotime($slot['end_time']);

                if (in_array($period, range($startSlot, $endSlot - 1))) {
                    return null;
                }
            }

            return date("H:i:s", $period);
        }, $periods);

    }

    return array_filter($periods);
}

$duration = 30;
$start = '09:00';
$end = '20:00';

$breakSlots = array(
    array(
        "start_time" => "12:00:00",
        "end_time" => "12:30:00"
    ),
    array(
        "start_time" => "15:00:00",
        "end_time" => "15:30:00",
    )
);

print_r(availableSlots($duration, $start, $end, $breakSlots));

```
###Output

```
Array
(
    [0] => 09:00:00
    [1] => 09:30:00
    [2] => 10:00:00
    [3] => 10:30:00
    [4] => 11:00:00
    [5] => 11:30:00
    [7] => 12:30:00
    [8] => 13:00:00
    [9] => 13:30:00
    [10] => 14:00:00
    [11] => 14:30:00
    [13] => 15:30:00
    [14] => 16:00:00
    [15] => 16:30:00
    [16] => 17:00:00
    [17] => 17:30:00
    [18] => 18:00:00
    [19] => 18:30:00
    [20] => 19:00:00
    [21] => 19:30:00
    [22] => 20:00:00
)
```
