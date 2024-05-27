import axios from '../../../src/common/helper/axios'

export const axiosTest = () => {
  describe('test axios functions', () => {
    const axiosConfig = {
      method: process.env.AXIOS_GET_STORAGE_METHOD,
      url: `${process.env.AXIOS_GET_STORAGE_URL}/test/1401/9/24/d4dcd5e2-eb8a-41ea-a9a4-229f157a8c2d`,
    }

    test('sending request to an wrong api', async () => {
      axiosConfig.url = '23mnb42hjg34j2gj34gh2hj34'

      const somethingSpy = jest.spyOn(axios, 'sendRequest')
      somethingSpy(axiosConfig)

      const data = await axios.sendRequest(axiosConfig)

      expect(data).toEqual(null)
    })
  })
}
