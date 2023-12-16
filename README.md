import heapq

class Process:
    def __init__(self, arrival_time, burst_time, process_id, queue_number):
        self.arrival_time = arrival_time
        self.burst_time = burst_time
        self.remaining_burst_time = burst_time
        self.process_id = process_id
        self.queue_number = queue_number

    def __lt__(self, other):
        # Comparison function for the priority queue (SJF)
        return self.remaining_burst_time < other.remaining_burst_time

class MLQScheduler:
    def __init__(self):
        self.sjf_queue = []  # Priority queue for SJF
        self.fcfs_queue = []  # Regular queue for FCFS
        self.time = 0
        self.process_counter = 0

    def add_process(self, arrival_time, burst_time, queue_number):
        process = Process(arrival_time, burst_time, self.process_counter, queue_number)
        self.process_counter += 1
        # Initially, all processes are added to the FCFS queue
        self.fcfs_queue.append(process)

    def execute_process(self, process):
        print(f"Time {self.time}: Executing Process {process.process_id} from Queue {process.queue_number}")

    def run_scheduler(self):
        while self.sjf_queue or self.fcfs_queue:
            # Check for processes arriving at the current time
            for process in self.fcfs_queue:
                if process.arrival_time <= self.time:
                    self.sjf_queue.append(process)
            self.fcfs_queue = [process for process in self.fcfs_queue if process.arrival_time > self.time]

            if self.sjf_queue:
                # Execute process based on SJF
                process = heapq.heappop(self.sjf_queue)
                self.execute_process(process)
                process.remaining_burst_time -= 1
                if process.remaining_burst_time > 0:
                    heapq.heappush(self.sjf_queue, process)
            elif self.fcfs_queue:
                # Execute process based on FCFS if SJF queue is empty
                process = self.fcfs_queue.pop(0)
                self.execute_process(process)
                process.remaining_burst_time -= 1
                if process.remaining_burst_time > 0:
                    self.fcfs_queue.append(process)

            self.time += 1

if __name__ == "__main__":
    scheduler = MLQScheduler()

    # Example Processes: (arrival_time, burst_time, queue_number)
    scheduler.add_process(0, 4, 0)
    scheduler.add_process(1, 5, 1)
    scheduler.add_process(2, 3, 0)
    scheduler.add_process(3, 6, 2)
    scheduler.add_process(4, 2, 1)

    scheduler.run_scheduler()
