# time-slot-with-braks-array
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
