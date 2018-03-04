#include <iostream>
#include <fstream>
#include <sstream>
#include <string>

using namespace std;

class matrix_t
{
private:
    int** data;
    unsigned int str;
    unsigned int collumns;

public:
    matrix_t()
    {
        data = nullptr;
        str = 0;
        collumns = 0;
    }

    matrix_t(matrix_t const& other)
    {
        this->str = other.str;
        this->collumns = other.collumns;
        this->data = new int*[this->str];
        for (unsigned int i = 0; i < this->str; i++)
        {
            data[i] = new int[this->collumns];
            for (unsigned int j = 0; j < this->collumns; j++)
            {
                this->data[i][j] = other.data[i][j];
            }
        }
    }

    matrix_t& operator=(matrix_t const& other)
    {
        for (unsigned int i = 0; i < str; i++)
        {
            delete[] this->data[i];
        }
        delete[] this->data;

        this->str = other.str;
        this->collumns = other.collumns;
        this->data = new int*[str];
        for (unsigned int i = 0; i < this->str; i++)
        {
            this->data[i] = new int[this->collumns];
            for (unsigned int j = 0; j < this->collumns; j++)
            {
                this->data[i][j] = other.data[i][j];
            }
        }
        return *this;
    }

    matrix_t add(matrix_t& other) const
    {
        matrix_t result;

        if (this->str == other.str && this->collumns == other.collumns)
        {
            result.data = new int*[this->str];
            for (unsigned int j = 0; j < this->str; j++)
            {
                result.data[j] = new int[this->collumns];
            }
            result.str = this->str;
            result.collumns = this->collumns;
            for (unsigned int i = 0; i < this->str; i++)
            {
                for (unsigned int j = 0; j < this->collumns; j++)
                {
                    result.data[i][j] = this->data[i][j] + other.data[i][j];
                }
            }
        }
        else
        {
            cout << endl << "Wrong size";
            exit(0);
        }
        return result;
    }

    matrix_t sub(matrix_t& other) const
    {
        matrix_t result;

        if (this->str == other.str && this->collumns == other.collumns)
        {
            result.data = new int*[this->str];
            for (unsigned int i = 0; i < this->str; i++)
            {
                result.data[i] = new int[this->collumns];
            }
            result.str = this->str;
            result.collumns = this->collumns;
            for (unsigned int i = 0; i < this->str; i++)
            {
                for (unsigned int j = 0; j < this->collumns; j++)
                {
                    result.data[i][j] = this->data[i][j] - other.data[i][j];
                }
            }
        }
        else
        {
            cout << endl << "Wrong size";
            exit(0);
        }
        return result;
    }

    matrix_t mul(matrix_t& other) const
    {
        matrix_t result;
        if (this->collumns == other.str)
        {
            result.str = this->str;
            result.collumns = other.collumns;
            result.data = new int*[this->str];
            for (unsigned int i = 0; i < this->str; ++i)
            {
                result.data[i] = new int[other.collumns];
            }
            for (unsigned int i = 0; i < this->str; i++)
            {
                for (unsigned int j = 0; j < other.collumns; j++)
                {
                    int res = 0;
                    for (unsigned int k = 0; k < this->collumns; k++)
                    {
                        res += this->data[i][k] * other.data[k][j];
                    }
                    result.data[i][j] = res;
                }
            }
        }
        else
        {
            cout << endl << "Wrong size";
            exit(0);
        }
        return result;
    }

    matrix_t trans(matrix_t& other) const
    {
        matrix_t result;
        result.str = other.collumns;
        result.collumns = other.str;
        result.data = new int*[other.collumns];
        for (unsigned int i = 0; i < other.collumns; i++)
        {
            result.data[i] = new int[other.str];
        }
        for (unsigned int i = 0; i < result.str; ++i)
        {
            for (unsigned int j = 0; j < result.collumns; ++j)
            {
                result.data[i][j] = other.data[j][i];
            }
        }
        return result;
    }

    ifstream& read(std::ifstream& fin)
    {
        char op;
        string line;
        getline(fin, line);
        istringstream stream(line);
        if (stream >> this->str && stream >> op && op == ',' && stream >> this->collumns)
        {
            this->data = new int*[this->str];
            for (unsigned int i = 0; i < this->str; i++)
            {
                this->data[i] = new int[this->collumns];
                for (unsigned int j = 0; j < this->collumns; j++)
                {
                    fin >> this->data[i][j];
                }
            }
        }
        else
        {
            cout << "An error has occured while reading input data";
            exit(0);
        }
        fin.close();
        return fin;
    }

    ostream& write(ostream& stream)
    {
        for (unsigned int i = 0; i < this->str; i++)
        {
            for (unsigned int j = 0; j < this->collumns; j++)
            {
                stream << this->data[i][j] << ' ';
            }
            stream << endl;
        }
        return stream;
    }

    ~matrix_t()
    {
        for (unsigned int i = 0; i < this->str; i++)
        {
            delete[] this->data[i];
        }
    }
};

bool file_name(string& file1, char& op, string& file2)
{
    string line;
    getline(cin, line);
    istringstream ptk(line);

    if (ptk >> file1 && ptk >> op)
    {
        return true;
    }
    else
    {
        if (ptk >> file2)
        {
            return true;
        }
    }
    return false;
}


int main()
{
    matrix_t matrix1;
    matrix_t result;

    char op;
    string file1, file2;
    if (!(file_name(file1, op, file2)))
    {
        cout << "An error has occured while reading input data";
        cin.get();
        return 0;
    }
    ifstream fin;
    const char* str = file1.c_str();
    fin.open(str);
    if (!fin.is_open())
    {
        cerr << "An error has occured while reading input data" << endl;
        return 0;
    }
    matrix1.read(fin);
    if (op == 'T')
    {
        result = result.trans(matrix1);
    }
    else if (op == '+' || op == '-' || op == '*')
    {
        matrix_t matrix2;
        ifstream fin;
        const char* str = file2.c_str();
        fin.open(str);
        if (!fin.is_open())
        {
            cerr << "An error has occured while reading input data" << endl;
            return 0;
        }
        matrix2.read(fin);
        switch (op)
        {
            case '+':
                result = matrix1.add(matrix2);
                break;
            case '-':
                result = matrix1.sub(matrix2);
                break;
            case '*':
                result = matrix1.mul(matrix2);
                break;
        }
    }
    result.write(cout);
    cin.get();
    return 0;
}
