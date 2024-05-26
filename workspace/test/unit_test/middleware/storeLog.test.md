import {createLog} from '../../../src/common/middlewares/storeLog'

export const storeLogTest = () => {
  describe('test storeLog functions', () => {
    const mockRequest = {
        headers: {
          apikey: '6d85b6d63f5e770b13c28e630ef6fee9704b854f05267ad794e6dfb17f43150f613a08c29b29add76737a76456022ed5254712f3ef82fb3be9f2f2f7b9d09c47',
          'user-agent': 'PostmanRuntime/7.29.2',
          accept: '*/*',
          'postman-token': 'c3b9c6a3-4f06-431a-a1ab-e6c61fdf2e78',
          host: 'localhost:3002',
          'accept-encoding': 'gzip, deflate, br',
          connection: 'keep-alive',
          'content-type': 'multipart/form-data; boundary=--------------------------046852673882800121915552',
          'content-length': '325193',
        },
        originalUrl: '/service/nationalCard/file',
        method: 'POST',
        api: '::1'
      },
      mockResponse = {},
      next = jest.fn()

    test('check api is valid in method GET if is not valid return not found or access denied', async () => {

      await createLog(mockRequest, mockResponse, next)
      
      expect(next).toHaveBeenCalled()
    })
  })
}
