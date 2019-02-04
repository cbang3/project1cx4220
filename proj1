#include <mpi.h>
#include <stdlib.h>
#include <math.h>
#include <sstream>
#include <vector>
#include <numeric>

using namespace std;

int dboard(int N) {
		//Throw darts at dartboard
		//Generate random numbers for X and Y coordinates
		//Check whether dart lands in square

		//radius = 1

	int M = 0;
	for (int i=0; i<N; i++) {

		float a = static_cast <float> (rand()) / static_cast <float> (RAND_MAX);
		a = sqrt(a);
		
		float theta = static_cast <float> (rand()) / (static_cast <float> (RAND_MAX/(2*M_PI)));

		float x = a * cos(theta);
		float y = a * sin(theta);

		if ((fabs(x) <= 1/sqrt(2)) && (fabs(y) <= 1/sqrt(2))) {
			M++;
		}
	}
	return M;
}

int main(int argc, char *argv[]) {

	//Initialize MPI
	MPI_Init(&argc, &argv);
	MPI_Comm comm = MPI_COMM_WORLD;

	//Change input args to int
	int N, R;
	stringstream arg1(argv[1]);
	arg1 >> N;
	stringstream arg2(argv[2]);
	arg2 >> R;

	int p, rank;
	//p = # of processors, rank = rank of the processor
	MPI_Comm_size(comm, &p);
	MPI_Comm_rank(comm, &rank);

	//Set random seed
	srand(rank);

	//n = number of darts each processor needs to throw
	int n;
	if (rank == 0) {
		n = ceil(N/p);
	} 

	//Broadcast to all processors
	MPI_Bcast(&n, 1, MPI_INT, 0, comm);

	//M_sum = # of darts that landed inside the square
	//v = vector of M_sums to be averaged later
	int M, M_sum;
	vector<float> v;

	//Start time measurement
	double start = MPI_Wtime();

	//For R # of times, each processor throw darts. Root processor collects it and average the R trials.
	for (int i=0; i < R; i++) {
		M = dboard(n);
		MPI_Reduce(&M, &M_sum, 1, MPI_INT, MPI_SUM, 0, comm);
		if (rank==0) {
			float pi = 2 * static_cast <float> (N) / static_cast <float> (M_sum);
			v.push_back(pi);
		}
	}

	//Sum of pi values are averaged for R trials
	float averagePI = accumulate(v.begin(), v.end(), 0.0)/v.size();

	//End time measurement
	double end = MPI_Wtime();
	double execTime = end - start;

	if (rank == 0) {
		//Print output
		//cout << "N= " << N << ", R= " << R << ", P= " << p << ", PI= " << averagePI << endl;
		printf("N = %d, R = %d, P = %d, PI = %f \n", N, R, p, averagePI);
		//cout << "Time= " << execTime << " seconds" << endl;
		printf("Time = %f seconds \n", execTime);
	}
	

	MPI_Finalize();
	return 0;
}
